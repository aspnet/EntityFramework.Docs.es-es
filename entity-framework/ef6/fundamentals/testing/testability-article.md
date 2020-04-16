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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="a2d46-102">Teestabilidad y Entity Framework 4.0</span><span class="sxs-lookup"><span data-stu-id="a2d46-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="a2d46-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="a2d46-103">Scott Allen</span></span>

<span data-ttu-id="a2d46-104">Publicación: mayo de 2010</span><span class="sxs-lookup"><span data-stu-id="a2d46-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="a2d46-105">Introducción</span><span class="sxs-lookup"><span data-stu-id="a2d46-105">Introduction</span></span>

<span data-ttu-id="a2d46-106">En este artículo técnico se describe y se muestra cómo escribir código comprobable con ADO.NET Entity Framework 4.0 y Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="a2d46-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="a2d46-107">Este artículo no trata de centrarse en una metodología de prueba específica, como el diseño basado en pruebas (TDD) o el diseño basado en el comportamiento (BDD).</span><span class="sxs-lookup"><span data-stu-id="a2d46-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="a2d46-108">En su lugar, este artículo se centrará en cómo escribir código que usa el ADO.NET Entity Framework, pero sigue siendo fácil de aislar y probar de forma automatizada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="a2d46-109">Veremos los patrones de diseño comunes que facilitan las pruebas en escenarios de acceso a datos y veremos cómo aplicar esos patrones al usar el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="a2d46-110">También veremos las características específicas del marco de trabajo para ver cómo esas características pueden funcionar en código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="a2d46-111">¿Qué es el código comprobable?</span><span class="sxs-lookup"><span data-stu-id="a2d46-111">What Is Testable Code?</span></span>

<span data-ttu-id="a2d46-112">La capacidad de verificar una pieza de software mediante pruebas unitarias automatizadas ofrece muchos beneficios deseables.</span><span class="sxs-lookup"><span data-stu-id="a2d46-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="a2d46-113">Todo el mundo sabe que las buenas pruebas reducirán el número de defectos de software en una aplicación y aumentarán la calidad de la aplicación, pero tener pruebas unitarias en su lugar va mucho más allá de simplemente encontrar errores.</span><span class="sxs-lookup"><span data-stu-id="a2d46-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="a2d46-114">Un buen conjunto de pruebas unitarias permite a un equipo de desarrollo ahorrar tiempo y mantener el control del software que crean.</span><span class="sxs-lookup"><span data-stu-id="a2d46-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="a2d46-115">Un equipo puede realizar cambios en el código existente, refactorizar, rediseñar y reestructurar el software para cumplir los nuevos requisitos y agregar nuevos componentes a una aplicación, todo ello sabiendo que el conjunto de pruebas puede comprobar el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2d46-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="a2d46-116">Las pruebas unitarias forman parte de un ciclo de retroalimentación rápida para facilitar el cambio y preservar la capacidad de mantenimiento del software a medida que aumenta la complejidad.</span><span class="sxs-lookup"><span data-stu-id="a2d46-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="a2d46-117">Sin embargo, las pruebas unitarias tienen un precio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="a2d46-118">Un equipo tiene que invertir el tiempo para crear y mantener pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="a2d46-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="a2d46-119">La cantidad de esfuerzo necesario para crear estas pruebas está directamente relacionada con la capacidad de **prueba** del software subyacente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="a2d46-120">¿Qué tan fácil es probar el software?</span><span class="sxs-lookup"><span data-stu-id="a2d46-120">How easy is the software to test?</span></span> <span data-ttu-id="a2d46-121">Un equipo que diseña software con capacidad de prueba en mente creará pruebas efectivas más rápido que el equipo que trabaja con software no comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="a2d46-122">Microsoft diseñó el ADO.NET Entity Framework 4.0 (EF4) teniendo en cuenta la capacidad de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="a2d46-123">Esto no significa que los desarrolladores escribirán pruebas unitarias en el propio código del marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="a2d46-124">En su lugar, los objetivos de capacidad de prueba para EF4 facilitan la creación de código comprobable que se basa en el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="a2d46-125">Antes de ver ejemplos específicos, vale la pena comprender las cualidades del código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="a2d46-126">Las cualidades del código comprobable</span><span class="sxs-lookup"><span data-stu-id="a2d46-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="a2d46-127">El código que es fácil de probar siempre mostrará al menos dos rasgos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="a2d46-128">En primer lugar, el código comprobable es fácil de **observar.**</span><span class="sxs-lookup"><span data-stu-id="a2d46-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="a2d46-129">Dado algún conjunto de entradas, debe ser fácil observar la salida del código.</span><span class="sxs-lookup"><span data-stu-id="a2d46-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="a2d46-130">Por ejemplo, probar el siguiente método es fácil porque el método devuelve directamente el resultado de un cálculo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="a2d46-131">Probar un método es difícil si el método escribe el valor calculado en un socket de red, una tabla de base de datos o un archivo como el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="a2d46-132">La prueba tiene que realizar un trabajo adicional para recuperar el valor.</span><span class="sxs-lookup"><span data-stu-id="a2d46-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="a2d46-133">En segundo lugar, el código comprobable es fácil **de aislar.**</span><span class="sxs-lookup"><span data-stu-id="a2d46-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="a2d46-134">Vamos a usar el siguiente pseudo-código como un mal ejemplo de código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="a2d46-135">El método es fácil de observar: podemos aprobar una póliza de seguro y verificar que el valor de devolución coincide con un resultado esperado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="a2d46-136">Sin embargo, para probar el método necesitaremos tener una base de datos instalada con el esquema correcto y configurar el servidor SMTP en caso de que el método intente enviar un correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="a2d46-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="a2d46-137">La prueba unitaria solo desea comprobar la lógica de cálculo dentro del método, pero la prueba puede fallar porque el servidor de correo electrónico está sin conexión o porque el servidor de base de datos se movió.</span><span class="sxs-lookup"><span data-stu-id="a2d46-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="a2d46-138">Ambos errores no están relacionados con el comportamiento que la prueba desea verificar.</span><span class="sxs-lookup"><span data-stu-id="a2d46-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="a2d46-139">El comportamiento es difícil de aislar.</span><span class="sxs-lookup"><span data-stu-id="a2d46-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="a2d46-140">Los desarrolladores de software que se esfuerzan por escribir código comprobable a menudo se esfuerzan por mantener una separación de preocupaciones en el código que escriben.</span><span class="sxs-lookup"><span data-stu-id="a2d46-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="a2d46-141">El método anterior debe centrarse en los cálculos empresariales y delegar los detalles de implementación de la base de datos y el correo electrónico a otros componentes.</span><span class="sxs-lookup"><span data-stu-id="a2d46-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="a2d46-142">Robert C. Martin lo llama el Principio de Responsabilidad Única.</span><span class="sxs-lookup"><span data-stu-id="a2d46-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="a2d46-143">Un objeto debe encapsular una responsabilidad única y estrecha, como calcular el valor de una directiva.</span><span class="sxs-lookup"><span data-stu-id="a2d46-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="a2d46-144">Todos los demás trabajos de base de datos y notificación deben ser responsabilidad de algún otro objeto.</span><span class="sxs-lookup"><span data-stu-id="a2d46-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="a2d46-145">El código escrito de esta manera es más fácil de aislar porque se centra en una sola tarea.</span><span class="sxs-lookup"><span data-stu-id="a2d46-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="a2d46-146">En .NET tenemos las abstracciones que necesitamos para seguir el principio de responsabilidad única y lograr el aislamiento.</span><span class="sxs-lookup"><span data-stu-id="a2d46-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="a2d46-147">Podemos usar definiciones de interfaz y forzar el código para usar la abstracción de interfaz en lugar de un tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="a2d46-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="a2d46-148">Más adelante en este artículo veremos cómo un método como el mal ejemplo presentado anteriormente puede funcionar con interfaces que *parecen* hablar con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="a2d46-149">En el momento de la prueba, sin embargo, podemos sustituir una implementación ficticia que no habla con la base de datos, sino que contiene datos en la memoria.</span><span class="sxs-lookup"><span data-stu-id="a2d46-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="a2d46-150">Esta implementación ficticia aislará el código de problemas no relacionados en el código de acceso a datos o la configuración de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="a2d46-151">El aislamiento tiene beneficios adicionales.</span><span class="sxs-lookup"><span data-stu-id="a2d46-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="a2d46-152">El cálculo empresarial en el último método solo debe tardar unos milisegundos en ejecutarse, pero la prueba en sí misma puede ejecutarse durante varios segundos a medida que el código salta por la red y se comunica con varios servidores.</span><span class="sxs-lookup"><span data-stu-id="a2d46-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="a2d46-153">Las pruebas unitarias deben ejecutarse rápidamente para facilitar pequeños cambios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="a2d46-154">Las pruebas unitarias también deben ser repetibles y no fallar porque un componente no relacionado con la prueba tiene un problema.</span><span class="sxs-lookup"><span data-stu-id="a2d46-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="a2d46-155">Escribir código que sea fácil de observar y aislar significa que los desarrolladores tendrán más fácil escribir pruebas para el código, pasar menos tiempo esperando a que se ejecuten las pruebas y, lo que es más importante, dedicar menos tiempo a rastrear errores que no existen.</span><span class="sxs-lookup"><span data-stu-id="a2d46-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="a2d46-156">Esperemos que pueda apreciar los beneficios de las pruebas y comprender las cualidades que exhibe el código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="a2d46-157">Estamos a punto de abordar cómo escribir código que funciona con EF4 para guardar datos en una base de datos mientras permanecemos observables y fáciles de aislar, pero primero vamos a limitar nuestro enfoque para discutir diseños comprobables para el acceso a datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="a2d46-158">Patrones de diseño para la persistencia de datos</span><span class="sxs-lookup"><span data-stu-id="a2d46-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="a2d46-159">Los dos malos ejemplos presentados anteriormente tenían demasiadas responsabilidades.</span><span class="sxs-lookup"><span data-stu-id="a2d46-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="a2d46-160">El primer ejemplo incorrecto tenía que realizar un cálculo *y* escribir en un archivo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="a2d46-161">El segundo ejemplo incorrecto tenía que leer los datos de una base de datos *y* realizar un cálculo empresarial *y* enviar correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="a2d46-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="a2d46-162">Al diseñar métodos más pequeños que separan las preocupaciones y delegan la responsabilidad a otros componentes, logrará grandes avances hacia la escritura de código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="a2d46-163">El objetivo es crear funcionalidad mediante la composición de acciones a partir de abstracciones pequeñas y enfocadas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="a2d46-164">Cuando se trata de la persistencia de datos, las abstracciones pequeñas y enfocadas que estamos buscando son tan comunes que se han documentado como patrones de diseño.</span><span class="sxs-lookup"><span data-stu-id="a2d46-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="a2d46-165">El libro de Martin Fowler Patterns of Enterprise Application Architecture fue el primer trabajo en describir estos patrones en impresión.</span><span class="sxs-lookup"><span data-stu-id="a2d46-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="a2d46-166">Proporcionaremos una breve descripción de estos patrones en las secciones siguientes antes de mostrar cómo estos ADO.NET Entity Framework implementa y funciona con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="a2d46-167">The Repository Pattern (El modelo de repositorio)</span><span class="sxs-lookup"><span data-stu-id="a2d46-167">The Repository Pattern</span></span>

<span data-ttu-id="a2d46-168">Fowler dice que un repositorio "media entre el dominio y las capas de asignación de datos mediante una interfaz similar a una colección para acceder a objetos de dominio".</span><span class="sxs-lookup"><span data-stu-id="a2d46-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="a2d46-169">El objetivo del patrón de repositorio es aislar el código de las minucias del acceso a datos, y como vimos el aislamiento anterior es un rasgo necesario para la capacidad de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="a2d46-170">La clave del aislamiento es cómo el repositorio expone objetos mediante una interfaz similar a una colección.</span><span class="sxs-lookup"><span data-stu-id="a2d46-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="a2d46-171">La lógica que escriba para utilizar el repositorio no tiene idea de cómo el repositorio materializará los objetos que solicite.</span><span class="sxs-lookup"><span data-stu-id="a2d46-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="a2d46-172">El repositorio puede hablar con una base de datos o simplemente devolver objetos de una colección en memoria.</span><span class="sxs-lookup"><span data-stu-id="a2d46-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="a2d46-173">Todo lo que el código debe saber es que el repositorio parece mantener la colección y puede recuperar, agregar y eliminar objetos de la colección.</span><span class="sxs-lookup"><span data-stu-id="a2d46-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="a2d46-174">En las aplicaciones .NET existentes, un repositorio concreto a menudo hereda de una interfaz genérica como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="a2d46-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="a2d46-175">Realizaremos algunos cambios en la definición de interfaz cuando proporcionemos una implementación para EF4, pero el concepto básico sigue siendo el mismo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="a2d46-176">El código puede usar un repositorio concreto que implementa esta interfaz para recuperar una entidad por su valor de clave principal, para recuperar una colección de entidades basadas en la evaluación de un predicado o simplemente recuperar todas las entidades disponibles.</span><span class="sxs-lookup"><span data-stu-id="a2d46-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="a2d46-177">El código también puede agregar y quitar entidades a través de la interfaz del repositorio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="a2d46-178">Dado un IRepository de Employee objetos, el código puede realizar las siguientes operaciones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="a2d46-179">Puesto que el código está utilizando una interfaz (IRepository of Employee), podemos proporcionar el código con diferentes implementaciones de la interfaz.</span><span class="sxs-lookup"><span data-stu-id="a2d46-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="a2d46-180">Una implementación podría ser una implementación respaldada por EF4 y la conservación de objetos en una base de datos de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a2d46-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="a2d46-181">Una implementación diferente (una que usamos durante las pruebas) podría estar respaldada por una lista en memoria de objetos Employee.</span><span class="sxs-lookup"><span data-stu-id="a2d46-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="a2d46-182">La interfaz ayudará a lograr el aislamiento en el código.</span><span class="sxs-lookup"><span data-stu-id="a2d46-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="a2d46-183">Observe que&lt;la&gt; interfaz IRepository T no expone una operación Save.</span><span class="sxs-lookup"><span data-stu-id="a2d46-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="a2d46-184">¿Cómo actualizamos los objetos existentes?</span><span class="sxs-lookup"><span data-stu-id="a2d46-184">How do we update existing objects?</span></span> <span data-ttu-id="a2d46-185">Es posible que se encuentre con definiciones de IRepository que incluyen la operación Guardar y las implementaciones de estos repositorios tendrán que conservar inmediatamente un objeto en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="a2d46-186">Sin embargo, en muchas aplicaciones no queremos conservar objetos individualmente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="a2d46-187">En su lugar, queremos dar vida a objetos, tal vez desde repositorios diferentes, modificar esos objetos como parte de una actividad empresarial y, a continuación, conservar todos los objetos como parte de una sola operación atómica.</span><span class="sxs-lookup"><span data-stu-id="a2d46-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="a2d46-188">Afortunadamente, hay un patrón para permitir este tipo de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="a2d46-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="a2d46-189">El patrón de unidad de trabajo</span><span class="sxs-lookup"><span data-stu-id="a2d46-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="a2d46-190">Fowler dice que una unidad de trabajo "mantendrá una lista de objetos afectados por una transacción comercial y coordina la escritura de los cambios y la resolución de problemas de simultaneidad".</span><span class="sxs-lookup"><span data-stu-id="a2d46-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="a2d46-191">Es responsabilidad de la unidad de trabajo realizar un seguimiento de los cambios en los objetos que damos vida desde un repositorio y conservar los cambios que hayamos realizado en los objetos cuando le decimos a la unidad de trabajo que confirme los cambios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="a2d46-192">También es responsabilidad de la unidad de trabajo tomar los nuevos objetos que hemos agregado a todos los repositorios e insertar los objetos en una base de datos, y también la eliminación de la administración.</span><span class="sxs-lookup"><span data-stu-id="a2d46-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="a2d46-193">Si alguna vez ha realizado algún trabajo con ADO.NET DataSets, ya estará familiarizado con la unidad de patrón de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="a2d46-194">ADO.NET DataSets tenía la capacidad de realizar un seguimiento de nuestras actualizaciones, eliminaciones e inserción de objetos DataRow y podía (con la ayuda de un TableAdapter) conciliar todos nuestros cambios en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="a2d46-195">Sin embargo, dataSet objetos modelan un subconjunto desconectado de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="a2d46-196">La unidad de patrón de trabajo muestra el mismo comportamiento, pero funciona con objetos de negocio y objetos de dominio que están aislados del código de acceso a datos y que no son conscientes de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="a2d46-197">Una abstracción para modelar la unidad de trabajo en código .NET podría tener el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="a2d46-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="a2d46-198">Al exponer las referencias de repositorio de la unidad de trabajo, podemos garantizar que una sola unidad de objeto de trabajo tenga la capacidad de realizar un seguimiento de todas las entidades materializadas durante una transacción comercial.</span><span class="sxs-lookup"><span data-stu-id="a2d46-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="a2d46-199">La implementación del método Commit para una unidad de trabajo real es donde sucede toda la magia para conciliar los cambios en memoria con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="a2d46-200">Dada una referencia IUnitOfWork, el código puede realizar cambios en los objetos de negocio recuperados de uno o varios repositorios y guardar todos los cambios mediante la operación de confirmación atómica.</span><span class="sxs-lookup"><span data-stu-id="a2d46-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="a2d46-201">El patrón de carga diferida</span><span class="sxs-lookup"><span data-stu-id="a2d46-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="a2d46-202">Fowler utiliza el nombre lazy load para describir "un objeto que no contiene todos los datos que necesita pero sabe cómo obtenerlo".</span><span class="sxs-lookup"><span data-stu-id="a2d46-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="a2d46-203">La carga diferida transparente es una característica importante que debe tener al escribir código de negocio comprobable y trabajar con una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="a2d46-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="a2d46-204">Por ejemplo, considere el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="a2d46-205">¿Cómo se rellena la colección TimeCards?</span><span class="sxs-lookup"><span data-stu-id="a2d46-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="a2d46-206">Hay dos respuestas posibles.</span><span class="sxs-lookup"><span data-stu-id="a2d46-206">There are two possible answers.</span></span> <span data-ttu-id="a2d46-207">Una respuesta es que el repositorio de empleados, cuando se le pide que obtenga un empleado, emite una consulta para recuperar tanto al empleado como la información de la tarjeta de tiempo asociada del empleado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="a2d46-208">En las bases de datos relacionales, esto generalmente requiere una consulta con una cláusula JOIN y puede dar lugar a la recuperación de más información de la que necesita una aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2d46-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="a2d46-209">¿Qué sucede si la aplicación nunca necesita tocar la propiedad TimeCards?</span><span class="sxs-lookup"><span data-stu-id="a2d46-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="a2d46-210">Una segunda respuesta es cargar la propiedad TimeCards "bajo demanda".</span><span class="sxs-lookup"><span data-stu-id="a2d46-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="a2d46-211">Esta carga diferida es implícita y transparente para la lógica de negocios porque el código no invoca API especiales para recuperar información de tarjeta de tiempo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="a2d46-212">El código asume que la información de la tarjeta de tiempo está presente cuando es necesario.</span><span class="sxs-lookup"><span data-stu-id="a2d46-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="a2d46-213">Hay algo de magia implicada en la carga diferida que generalmente implica la interceptación en tiempo de ejecución de invocaciones de método.</span><span class="sxs-lookup"><span data-stu-id="a2d46-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="a2d46-214">El código de interceptación es responsable de hablar con la base de datos y recuperar información de la tarjeta de tiempo, dejando la lógica de negocios libre para ser lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="a2d46-215">Esta magia de carga diferida permite que el código de negocio se aísle de las operaciones de recuperación de datos y da como resultado código más comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="a2d46-216">El inconveniente de una carga diferida es que cuando una *aplicación* necesita la información de la tarjeta de tiempo, el código ejecutará una consulta adicional.</span><span class="sxs-lookup"><span data-stu-id="a2d46-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="a2d46-217">Esto no es una preocupación para muchas aplicaciones, pero para las aplicaciones sensibles al rendimiento o aplicaciones que recorren en bucle una serie de objetos de empleado y ejecutan una consulta para recuperar tarjetas de tiempo durante cada iteración del bucle (un problema a menudo denominado problema de consulta N+1), la carga diferida es un arrastre.</span><span class="sxs-lookup"><span data-stu-id="a2d46-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="a2d46-218">En estos escenarios, es posible que una aplicación desee cargar con entusiasmo la información de la tarjeta de tiempo de la manera más eficaz posible.</span><span class="sxs-lookup"><span data-stu-id="a2d46-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="a2d46-219">Afortunadamente, veremos cómo EF4 admite cargas diferidas implícitas y cargas diligentes eficientes a medida que pasamos a la siguiente sección e implementamos estos patrones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="a2d46-220">Implementación de patrones con Entity Framework</span><span class="sxs-lookup"><span data-stu-id="a2d46-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="a2d46-221">La buena noticia es que todos los patrones de diseño que describimos en la última sección son fáciles de implementar con EF4.</span><span class="sxs-lookup"><span data-stu-id="a2d46-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="a2d46-222">Para demostrar que vamos a usar una sencilla aplicación ASP.NET MVC para editar y mostrar los empleados y su información de tarjeta de tiempo asociada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="a2d46-223">Comenzaremos usando los siguientes "objetos CLR antiguos sin formato" (POCO).</span><span class="sxs-lookup"><span data-stu-id="a2d46-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="a2d46-224">Estas definiciones de clase cambiarán ligeramente a medida que exploramos diferentes enfoques y características de EF4, pero la intención es mantener estas clases lo más pernopeso ignorante (PI) como sea posible.</span><span class="sxs-lookup"><span data-stu-id="a2d46-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="a2d46-225">Un objeto PI no sabe *cómo*, o incluso *si*, el estado que contiene vive dentro de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="a2d46-226">PI y POCOs van de la mano con software comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="a2d46-227">Los objetos que utilizan un enfoque POCO son menos restringidos, más flexibles y más fáciles de probar porque pueden funcionar sin una base de datos presente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="a2d46-228">Con los POCO en su lugar podemos crear un Entity Data Model (EDM) en Visual Studio (consulte la figura 1).</span><span class="sxs-lookup"><span data-stu-id="a2d46-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="a2d46-229">No usaremos el EDM para generar código para nuestras entidades.</span><span class="sxs-lookup"><span data-stu-id="a2d46-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="a2d46-230">En su lugar, queremos utilizar las entidades que elaboramos con amor a mano.</span><span class="sxs-lookup"><span data-stu-id="a2d46-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="a2d46-231">Solo usaremos el EDM para generar nuestro esquema de base de datos y proporcionar los metadatos que EF4 necesita para asignar objetos a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="a2d46-233">**Figura 1**</span><span class="sxs-lookup"><span data-stu-id="a2d46-233">**Figure 1**</span></span>

<span data-ttu-id="a2d46-234">Nota: si desea desarrollar primero el modelo EDM, es posible generar código POCO limpio desde el EDM.</span><span class="sxs-lookup"><span data-stu-id="a2d46-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="a2d46-235">Puede hacerlo con una extensión de Visual Studio 2010 proporcionada por el equipo de programación de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="a2d46-236">Para descargar la extensión, inicie el Administrador de extensiones desde el menú Herramientas de Visual Studio y busque "POCO" en la galería en línea de plantillas (consulte la figura 2).</span><span class="sxs-lookup"><span data-stu-id="a2d46-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="a2d46-237">Hay varias plantillas POCO disponibles para EF.</span><span class="sxs-lookup"><span data-stu-id="a2d46-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="a2d46-238">Para obtener más información sobre el uso de la plantilla, vea ["Tutorial: Plantilla POCO para Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".</span><span class="sxs-lookup"><span data-stu-id="a2d46-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="a2d46-240">**Figura 2**</span><span class="sxs-lookup"><span data-stu-id="a2d46-240">**Figure 2**</span></span>

<span data-ttu-id="a2d46-241">Desde este punto de partida poco exploraremos dos enfoques diferentes para el código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="a2d46-242">El primer enfoque que llamo el enfoque EF porque aprovecha las abstracciones de la API de Entity Framework para implementar unidades de trabajo y repositorios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="a2d46-243">En el segundo enfoque crearemos nuestras propias abstracciones de repositorio personalizadas y luego veremos las ventajas y desventajas de cada enfoque.</span><span class="sxs-lookup"><span data-stu-id="a2d46-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="a2d46-244">Comenzaremos explorando el enfoque de EF.</span><span class="sxs-lookup"><span data-stu-id="a2d46-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="a2d46-245">Una implementación de EF Centric</span><span class="sxs-lookup"><span data-stu-id="a2d46-245">An EF Centric Implementation</span></span>

<span data-ttu-id="a2d46-246">Considere la siguiente acción de controlador de un proyecto de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="a2d46-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="a2d46-247">La acción recupera un objeto Employee y devuelve un resultado para mostrar una vista detallada del empleado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="a2d46-248">¿Se puede probar el código?</span><span class="sxs-lookup"><span data-stu-id="a2d46-248">Is the code testable?</span></span> <span data-ttu-id="a2d46-249">Hay al menos dos pruebas que necesitamos para verificar el comportamiento de la acción.</span><span class="sxs-lookup"><span data-stu-id="a2d46-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="a2d46-250">En primer lugar, nos gustaría comprobar que la acción devuelve la vista correcta, una prueba fácil.</span><span class="sxs-lookup"><span data-stu-id="a2d46-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="a2d46-251">También queremos escribir una prueba para comprobar que la acción recupera el empleado correcto y nos gustaría hacerlo sin ejecutar código para consultar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="a2d46-252">Recuerde que queremos aislar el código bajo prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="a2d46-253">El aislamiento garantizará que la prueba no falle debido a un error en el código de acceso a datos o en la configuración de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="a2d46-254">Si la prueba falla, sabremos que tenemos un error en la lógica del controlador, y no en algún componente del sistema de nivel inferior.</span><span class="sxs-lookup"><span data-stu-id="a2d46-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="a2d46-255">Para lograr el aislamiento necesitaremos algunas abstracciones como las interfaces que presentamos anteriormente para repositorios y unidades de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="a2d46-256">Recuerde que el patrón de repositorio está diseñado para mediar entre los objetos de dominio y la capa de asignación de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="a2d46-257">En este escenario EF4 *es* la capa de asignación de datos y&lt;&gt; ya proporciona una abstracción similar a un repositorio denominada IObjectSet T (desde el espacio de nombres System.Data.Objects).</span><span class="sxs-lookup"><span data-stu-id="a2d46-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="a2d46-258">La definición de interfaz es similar a la siguiente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="a2d46-259">IObjectSet&lt;&gt; T cumple los requisitos de un repositorio porque se&lt;asemeja&gt;a una colección de objetos (a través de IEnumerable T ) y proporciona métodos para agregar y quitar objetos de la colección simulada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="a2d46-260">Los métodos Attach y Detach exponen capacidades adicionales de la API de EF4.</span><span class="sxs-lookup"><span data-stu-id="a2d46-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="a2d46-261">Para utilizar IObjectSet&lt;T&gt; como interfaz para repositorios necesitamos una unidad de abstracción de trabajo para enlazar repositorios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="a2d46-262">Una implementación concreta de esta interfaz se hablará con SQL Server y es fácil de crear mediante la clase ObjectContext de EF4.</span><span class="sxs-lookup"><span data-stu-id="a2d46-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="a2d46-263">La clase ObjectContext es la verdadera unidad de trabajo de la API de EF4.</span><span class="sxs-lookup"><span data-stu-id="a2d46-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="a2d46-264">Dar vida a&lt;&gt; un IObjectSet T es tan fácil como invocar el método CreateObjectSet del objeto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="a2d46-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="a2d46-265">En segundo plano, el marco de trabajo utilizará los metadatos&lt;que&gt;proporcionamos en el EDM para producir un ObjectSet T concreto.</span><span class="sxs-lookup"><span data-stu-id="a2d46-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="a2d46-266">Seguiremos con devolver la interfaz&lt;&gt; IObjectSet T porque ayudará a conservar la capacidad de prueba en el código de cliente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="a2d46-267">Esta implementación concreta es útil en la producción, pero tenemos que centrarnos en cómo usaremos nuestra abstracción IUnitOfWork para facilitar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="a2d46-268">Los dobles de la prueba</span><span class="sxs-lookup"><span data-stu-id="a2d46-268">The Test Doubles</span></span>

<span data-ttu-id="a2d46-269">Para aislar la acción del controlador necesitaremos la capacidad de cambiar entre la unidad de trabajo real (respaldada por un ObjectContext) y una unidad de trabajo doble o "falsa" de prueba (realizando operaciones en memoria).</span><span class="sxs-lookup"><span data-stu-id="a2d46-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="a2d46-270">El enfoque común para realizar este tipo de conmutación es no permitir que el controlador MVC crear una instancia de una unidad de trabajo, sino pasar la unidad de trabajo en el controlador como un parámetro constructor.</span><span class="sxs-lookup"><span data-stu-id="a2d46-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="a2d46-271">El código anterior es un ejemplo de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="a2d46-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="a2d46-272">No permitimos que el controlador cree su dependencia (la unidad de trabajo) sino que inyecte la dependencia en el controlador.</span><span class="sxs-lookup"><span data-stu-id="a2d46-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="a2d46-273">En un proyecto MVC es común usar un generador de controladores personalizados en combinación con un contenedor de inversión de control (IoC) para automatizar la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="a2d46-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="a2d46-274">Estos temas están fuera del ámbito de este artículo, pero puede leer más siguiendo las referencias al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="a2d46-275">Una unidad falsa de implementación de trabajo que podemos usar para las pruebas podría tener el siguiente aspecto.</span><span class="sxs-lookup"><span data-stu-id="a2d46-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="a2d46-276">Observe que la unidad de trabajo falsa expone una propiedad Commited.</span><span class="sxs-lookup"><span data-stu-id="a2d46-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="a2d46-277">A veces es útil agregar características a una clase falsa que faciliten las pruebas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="a2d46-278">En este caso, es fácil observar si el código confirma una unidad de trabajo comprobando la propiedad Commited.</span><span class="sxs-lookup"><span data-stu-id="a2d46-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="a2d46-279">También necesitaremos un IObjectSet&lt;&gt; T falso para mantener los objetos Employee y TimeCard en la memoria.</span><span class="sxs-lookup"><span data-stu-id="a2d46-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="a2d46-280">Podemos proporcionar una sola implementación mediante genéricos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="a2d46-281">Esta prueba delega la mayor parte de&lt;&gt; su trabajo en un objeto HashSet T subyacente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="a2d46-282">Tenga en cuenta&lt;&gt; que IObjectSet T requiere una restricción genérica que aplica T como&lt;una&gt;clase (un tipo de referencia) y también nos obliga a implementar IQueryable T .</span><span class="sxs-lookup"><span data-stu-id="a2d46-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="a2d46-283">Es fácil hacer que una colección en memoria&lt;&gt; aparezca como un IQueryable T mediante el operador LINQ estándar AsQueryable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="a2d46-284">Las pruebas</span><span class="sxs-lookup"><span data-stu-id="a2d46-284">The Tests</span></span>

<span data-ttu-id="a2d46-285">Las pruebas unitarias tradicionales usarán una sola clase de prueba para contener todas las pruebas de todas las acciones en un único controlador MVC.</span><span class="sxs-lookup"><span data-stu-id="a2d46-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="a2d46-286">Podemos escribir estas pruebas, o cualquier tipo de prueba unitaria, usando las falsificaciones en memoria que hemos construido.</span><span class="sxs-lookup"><span data-stu-id="a2d46-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="a2d46-287">Sin embargo, para este artículo evitaremos el enfoque de clase de prueba monolítica y en su lugar agruparemos nuestras pruebas para centrarnos en una parte específica de la funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="a2d46-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="a2d46-288">Por ejemplo, "crear nuevo empleado" podría ser la funcionalidad que queremos probar, por lo que usaremos una sola clase de prueba para comprobar la acción de controlador único responsable de crear un nuevo empleado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="a2d46-289">Hay algún código de configuración común que necesitamos para todas estas clases de prueba de grano fino.</span><span class="sxs-lookup"><span data-stu-id="a2d46-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="a2d46-290">Por ejemplo, siempre necesitamos crear nuestros repositorios en memoria y una unidad de trabajo falsa.</span><span class="sxs-lookup"><span data-stu-id="a2d46-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="a2d46-291">También necesitamos una instancia del controlador del empleado con la unidad falsa de trabajo inyectada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="a2d46-292">Compartiremos este código de configuración común entre clases de prueba mediante una clase base.</span><span class="sxs-lookup"><span data-stu-id="a2d46-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="a2d46-293">El "objeto madre" que usamos en la clase base es un patrón común para crear datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="a2d46-294">Una madre de objeto contiene métodos de fábrica para crear instancias de entidades de prueba para su uso en varios accesorios de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="a2d46-295">Podemos usar EmployeeControllerTestBase como la clase base para una serie de accesorios de prueba (consulte la figura 3).</span><span class="sxs-lookup"><span data-stu-id="a2d46-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="a2d46-296">Cada accesorio de prueba probará una acción específica del controlador.</span><span class="sxs-lookup"><span data-stu-id="a2d46-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="a2d46-297">Por ejemplo, un accesorio de prueba se centrará en probar la acción Crear utilizada durante una solicitud HTTP GET (para mostrar la vista para crear un empleado) y un accesorio diferente se centrará en la acción Crear utilizada en una solicitud HTTP POST (para tomar la información enviada por el usuario para crear un empleado).</span><span class="sxs-lookup"><span data-stu-id="a2d46-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="a2d46-298">Cada clase derivada solo es responsable de la configuración necesaria en su contexto específico y de proporcionar las aserciones necesarias para comprobar los resultados de su contexto de prueba específico.</span><span class="sxs-lookup"><span data-stu-id="a2d46-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="a2d46-300">**Figura 3**</span><span class="sxs-lookup"><span data-stu-id="a2d46-300">**Figure 3**</span></span>

<span data-ttu-id="a2d46-301">La convención de nomenclatura y el estilo de prueba que se presentan aquí no son necesarios para el código comprobable: es solo un enfoque.</span><span class="sxs-lookup"><span data-stu-id="a2d46-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="a2d46-302">La Figura 4 muestra las pruebas que se ejecutan en el complemento Jet Brains Resharper test runner para Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="a2d46-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="a2d46-304">**Figura 4**</span><span class="sxs-lookup"><span data-stu-id="a2d46-304">**Figure 4**</span></span>

<span data-ttu-id="a2d46-305">Con una clase base para controlar el código de configuración compartido, las pruebas unitarias para cada acción del controlador son pequeñas y fáciles de escribir.</span><span class="sxs-lookup"><span data-stu-id="a2d46-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="a2d46-306">Las pruebas se ejecutarán rápidamente (ya que estamos realizando operaciones en memoria) y no deberían fallar debido a problemas ambientales o de infraestructura no relacionados (porque hemos aislado la unidad bajo prueba).</span><span class="sxs-lookup"><span data-stu-id="a2d46-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="a2d46-307">En estas pruebas, la clase base realiza la mayor parte del trabajo de configuración.</span><span class="sxs-lookup"><span data-stu-id="a2d46-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="a2d46-308">Recuerde que el constructor de la clase base crea el repositorio en memoria, una unidad de trabajo falsa y una instancia de la clase EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="a2d46-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="a2d46-309">La clase de prueba se deriva de esta clase base y se centra en los detalles de la prueba de la Create método.</span><span class="sxs-lookup"><span data-stu-id="a2d46-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="a2d46-310">En este caso, los detalles se reducen a los pasos de "organizar, actuar y afirmar" que verá en cualquier procedimiento de prueba unitaria:</span><span class="sxs-lookup"><span data-stu-id="a2d46-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="a2d46-311">Cree un objeto newEmployee para simular los datos entrantes.</span><span class="sxs-lookup"><span data-stu-id="a2d46-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="a2d46-312">Invoque la acción Crear de EmployeeController y pase el newEmployee.</span><span class="sxs-lookup"><span data-stu-id="a2d46-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="a2d46-313">Compruebe que la acción Crear produce los resultados esperados (el empleado aparece en el repositorio).</span><span class="sxs-lookup"><span data-stu-id="a2d46-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="a2d46-314">Lo que hemos creado nos permite probar cualquiera de las acciones de EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="a2d46-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="a2d46-315">Por ejemplo, cuando escribimos pruebas para la acción Index del controlador Employee podemos heredar de la clase base de prueba para establecer la misma configuración base para nuestras pruebas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="a2d46-316">De nuevo, la clase base creará el repositorio en memoria, la unidad de trabajo falsa y una instancia de EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="a2d46-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="a2d46-317">Las pruebas para la acción Index solo tienen que centrarse en invocar la acción Index y probar las cualidades del modelo que devuelve la acción.</span><span class="sxs-lookup"><span data-stu-id="a2d46-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="a2d46-318">Las pruebas que estamos creando con falsificaciones en memoria están orientadas a probar el *estado* del software.</span><span class="sxs-lookup"><span data-stu-id="a2d46-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="a2d46-319">Por ejemplo, al probar la acción Crear queremos inspeccionar el estado del repositorio después de ejecutar la acción de creación: ¿el repositorio contiene al nuevo empleado?</span><span class="sxs-lookup"><span data-stu-id="a2d46-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="a2d46-320">Más adelante veremos las pruebas basadas en la interacción.</span><span class="sxs-lookup"><span data-stu-id="a2d46-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="a2d46-321">Las pruebas basadas en interacciones preguntarán si el código sometido a prueba invocó los métodos adecuados en nuestros objetos y pasó los parámetros correctos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="a2d46-322">Por ahora vamos a mover en la cubierta otro patrón de diseño - la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="a2d46-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="a2d46-323">Carga ansiosa y carga diferida</span><span class="sxs-lookup"><span data-stu-id="a2d46-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="a2d46-324">En algún momento de la aplicación web ASP.NET MVC, es posible que deseemos mostrar la información de un empleado e incluir las tarjetas de tiempo asociadas del empleado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="a2d46-325">Por ejemplo, es posible que tengamos una visualización de resumen de tarjeta de tiempo que muestre el nombre del empleado y el número total de tarjetas de tiempo en el sistema.</span><span class="sxs-lookup"><span data-stu-id="a2d46-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="a2d46-326">Hay varios enfoques que podemos tomar para implementar esta característica.</span><span class="sxs-lookup"><span data-stu-id="a2d46-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="a2d46-327">Proyección</span><span class="sxs-lookup"><span data-stu-id="a2d46-327">Projection</span></span>

<span data-ttu-id="a2d46-328">Un enfoque fácil para crear el resumen es construir un modelo dedicado a la información que queremos mostrar en la vista.</span><span class="sxs-lookup"><span data-stu-id="a2d46-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="a2d46-329">En este escenario, el modelo podría tener el siguiente aspecto.</span><span class="sxs-lookup"><span data-stu-id="a2d46-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="a2d46-330">Tenga en cuenta que EmployeeSummaryViewModel no es una entidad, en otras palabras, no es algo que queremos conservar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="a2d46-331">Sólo vamos a usar esta clase para mezclar datos en la vista de una manera fuertemente tipada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="a2d46-332">El modelo de vista es como un objeto de transferencia de datos (DTO) porque no contiene ningún comportamiento (sin métodos) – solo propiedades.</span><span class="sxs-lookup"><span data-stu-id="a2d46-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="a2d46-333">Las propiedades contendrán los datos que necesitamos mover.</span><span class="sxs-lookup"><span data-stu-id="a2d46-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="a2d46-334">Es fácil crear instancias de este modelo de vista mediante el operador de proyección estándar de LINQ: el operador Select.</span><span class="sxs-lookup"><span data-stu-id="a2d46-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="a2d46-335">Hay dos características notables en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="a2d46-335">There are two notable features to the above code.</span></span> <span data-ttu-id="a2d46-336">En primer lugar, el código es fácil de probar porque todavía es fácil de observar y aislar.</span><span class="sxs-lookup"><span data-stu-id="a2d46-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="a2d46-337">El operador Select funciona tan bien contra nuestras falsificaciones en memoria como contra la unidad real de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="a2d46-338">La segunda característica notable es cómo el código permite que EF4 genere una sola consulta eficaz para ensamblar la información de los empleados y de la tarjeta de tiempo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="a2d46-339">Hemos cargado la información de los empleados y la información de la tarjeta de tiempo en el mismo objeto sin usar ninguna API especial.</span><span class="sxs-lookup"><span data-stu-id="a2d46-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="a2d46-340">El código simplemente expresa la información que requiere mediante operadores LINQ estándar que funcionan con orígenes de datos en memoria, así como orígenes de datos remotos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="a2d46-341">EF4 pudo traducir los árboles de expresión generados por la consulta LINQ y el compilador de C\# en una única y eficaz consulta T-SQL.</span><span class="sxs-lookup"><span data-stu-id="a2d46-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="a2d46-342">Hay otras ocasiones en las que no queremos trabajar con un modelo de vista o un objeto DTO, pero con entidades reales.</span><span class="sxs-lookup"><span data-stu-id="a2d46-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="a2d46-343">Cuando sabemos que necesitamos un empleado *y* las tarjetas de tiempo del empleado, podemos cargar con entusiasmo los datos relacionados de una manera discreta y eficiente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="a2d46-344">Carga explícita de Eager</span><span class="sxs-lookup"><span data-stu-id="a2d46-344">Explicit Eager Loading</span></span>

<span data-ttu-id="a2d46-345">Cuando queremos cargar con entusiasmo la información de entidad relacionada, necesitamos algún mecanismo para la lógica de negocios (o en este escenario, lógica de acción del controlador) para expresar su deseo al repositorio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="a2d46-346">La clase T&lt;&gt; de ObjectQuery de EF4 define un método Include para especificar los objetos relacionados que se van a recuperar durante una consulta.</span><span class="sxs-lookup"><span data-stu-id="a2d46-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="a2d46-347">Recuerde que EF4 ObjectContext expone entidades&lt;a&gt; través de la&lt;&gt;clase ObjectSet T concreta que hereda de ObjectQuery T .</span><span class="sxs-lookup"><span data-stu-id="a2d46-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="a2d46-348">Si estuviéramos usando referencias de ObjectSet&lt;T&gt; en nuestra acción de controlador podríamos escribir el siguiente código para especificar una carga diligente de información de tarjeta de tiempo para cada empleado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="a2d46-349">Sin embargo, puesto que estamos tratando de mantener&lt;&gt; nuestro código comprobable no estamos exponiendo ObjectSet T desde fuera de la unidad real de la clase de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="a2d46-350">En su lugar, nos&lt;basamos en la interfaz IObjectSet T&gt; que es más fácil de falsificar, pero IObjectSet&lt;T&gt; no define un método Include.</span><span class="sxs-lookup"><span data-stu-id="a2d46-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="a2d46-351">La belleza de LINQ LINQ es que podemos crear nuestro propio operador Include.</span><span class="sxs-lookup"><span data-stu-id="a2d46-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="a2d46-352">Observe que este operador Include se define&lt;como&gt; un método&lt;de&gt;extensión para IQueryable T en lugar de IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="a2d46-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="a2d46-353">Esto nos da la capacidad de utilizar el método con una&lt;&gt;gama más&lt;amplia&gt;de&lt;tipos&gt;posibles,&lt;incluidos&gt;IQueryable T , IObjectSet T , ObjectQuery T y ObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="a2d46-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="a2d46-354">En caso de que la secuencia subyacente no&lt;&gt;sea una verdadera Query T de EF4 , no hay ningún daño realizado y el operador Include no es op.</span><span class="sxs-lookup"><span data-stu-id="a2d46-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="a2d46-355">Si la secuencia *is* subyacente es&lt;&gt; un ObjectQuery T&lt;(o derivado de ObjectQuery T&gt;), EF4 verá nuestro requisito de datos adicionales y formulará la consulta SQL adecuada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="a2d46-356">Con este nuevo operador en su lugar podemos solicitar explícitamente una carga diligente de información de la tarjeta de tiempo del repositorio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="a2d46-357">Cuando se ejecuta en un ObjectContext real, el código genera la siguiente consulta única.</span><span class="sxs-lookup"><span data-stu-id="a2d46-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="a2d46-358">La consulta recopila suficiente información de la base de datos en un viaje para materializar los objetos de empleado y rellenar completamente su propiedad TimeCards.</span><span class="sxs-lookup"><span data-stu-id="a2d46-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="a2d46-359">La gran noticia es que el código dentro del método de acción sigue siendo totalmente comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="a2d46-360">No necesitamos proporcionar ninguna característica adicional para nuestras falsificaciones para apoyar al operador Include.</span><span class="sxs-lookup"><span data-stu-id="a2d46-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="a2d46-361">La mala noticia es que tuvimos que usar el operador Include dentro del código que queríamos mantener la persistencia ignorante.</span><span class="sxs-lookup"><span data-stu-id="a2d46-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="a2d46-362">Este es un ejemplo excelente del tipo de compensaciones que deberá evaluar al crear código comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="a2d46-363">Hay ocasiones en las que debe dejar que los problemas de persistencia se filtren fuera de la abstracción del repositorio para cumplir los objetivos de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="a2d46-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="a2d46-364">La alternativa a la carga diligente es la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="a2d46-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="a2d46-365">La carga diferida significa que *no* necesitamos nuestro código de negocio para anunciar explícitamente el requisito de los datos asociados.</span><span class="sxs-lookup"><span data-stu-id="a2d46-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="a2d46-366">En su lugar, usamos nuestras entidades en la aplicación y si se necesitan datos adicionales Entity Framework cargará los datos a petición.</span><span class="sxs-lookup"><span data-stu-id="a2d46-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="a2d46-367">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="a2d46-367">Lazy Loading</span></span>

<span data-ttu-id="a2d46-368">Es fácil imaginar un escenario en el que no sepamos qué datos necesitará una pieza de lógica empresarial.</span><span class="sxs-lookup"><span data-stu-id="a2d46-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="a2d46-369">Es posible que sepamos que la lógica necesita un objeto de empleado, pero podemos bifurcarnos en diferentes rutas de ejecución donde algunas de esas rutas requieren información de tarjeta de tiempo del empleado y otras no.</span><span class="sxs-lookup"><span data-stu-id="a2d46-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="a2d46-370">Escenarios como este son perfectos para la carga diferida implícita porque los datos aparecen mágicamente según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="a2d46-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="a2d46-371">La carga diferida, también conocida como carga diferida, coloca algunos requisitos en nuestros objetos de entidad.</span><span class="sxs-lookup"><span data-stu-id="a2d46-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="a2d46-372">Los POCO con verdadera ignorancia de persistencia no enfrentarían ningún requisito de la capa de persistencia, pero la verdadera ignorancia de persistencia es prácticamente imposible de lograr.</span><span class="sxs-lookup"><span data-stu-id="a2d46-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="a2d46-373">En su lugar, medimos la ignorancia de persistencia en grados relativos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="a2d46-374">Sería desafortunado si necesitábamos heredar de una clase base orientada a la persistencia o usar una colección especializada para lograr la carga diferida en LOS POCO.</span><span class="sxs-lookup"><span data-stu-id="a2d46-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="a2d46-375">Afortunadamente, EF4 tiene una solución menos intrusiva.</span><span class="sxs-lookup"><span data-stu-id="a2d46-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="a2d46-376">Prácticamente indetectable</span><span class="sxs-lookup"><span data-stu-id="a2d46-376">Virtually Undetectable</span></span>

<span data-ttu-id="a2d46-377">Cuando se utilizan objetos POCO, EF4 puede generar dinámicamente proxies en tiempo de ejecución para entidades.</span><span class="sxs-lookup"><span data-stu-id="a2d46-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="a2d46-378">Estos proxies envuelven de forma invisible los POCO materializados y proporcionan servicios adicionales interceptando cada propiedad get y set operation para realizar trabajo adicional.</span><span class="sxs-lookup"><span data-stu-id="a2d46-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="a2d46-379">Uno de estos servicios es la función de carga diferida que estamos buscando.</span><span class="sxs-lookup"><span data-stu-id="a2d46-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="a2d46-380">Otro servicio es un mecanismo de seguimiento de cambios eficaz que puede registrar cuando el programa cambia los valores de propiedad de una entidad.</span><span class="sxs-lookup"><span data-stu-id="a2d46-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="a2d46-381">ObjectContext utiliza la lista de cambios durante el método SaveChanges para conservar las entidades modificadas mediante comandos UPDATE.</span><span class="sxs-lookup"><span data-stu-id="a2d46-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="a2d46-382">Para que estos servidores proxy funcionen, sin embargo, necesitan una forma de enlazar a las operaciones get y set de propiedad en una entidad, y los servidores proxy logran este objetivo reemplazando a los miembros virtuales.</span><span class="sxs-lookup"><span data-stu-id="a2d46-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="a2d46-383">Por lo tanto, si queremos tener carga diferida implícita y seguimiento de cambios eficiente, tenemos que volver a nuestras definiciones de clase POCO y marcar las propiedades como virtuales.</span><span class="sxs-lookup"><span data-stu-id="a2d46-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="a2d46-384">Todavía podemos decir que la entidad Employee es principalmente ignorante de persistencia.</span><span class="sxs-lookup"><span data-stu-id="a2d46-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="a2d46-385">El único requisito es usar miembros virtuales y esto no afecta a la capacidad de prueba del código.</span><span class="sxs-lookup"><span data-stu-id="a2d46-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="a2d46-386">No necesitamos derivar de ninguna clase base especial, ni siquiera usar una colección especial dedicada a la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="a2d46-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="a2d46-387">Como se muestra en el código, cualquier clase que implementa ICollection&lt;T&gt; está disponible para contener entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="a2d46-388">También hay un cambio menor que necesitamos hacer dentro de nuestra unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="a2d46-389">La carga diferida está *desactivada* de forma predeterminada cuando se trabaja directamente con un objeto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="a2d46-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="a2d46-390">Hay una propiedad que podemos establecer en la propiedad ContextOptions para habilitar la carga diferida, y podemos establecer esta propiedad dentro de nuestra unidad de trabajo real si queremos habilitar la carga diferida en todas partes.</span><span class="sxs-lookup"><span data-stu-id="a2d46-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="a2d46-391">Con la carga diferida implícita habilitada, el código de aplicación puede usar un empleado y las tarjetas de tiempo asociadas del empleado sin dejar de tener conocimiento del trabajo necesario para que EF cargue los datos adicionales.</span><span class="sxs-lookup"><span data-stu-id="a2d46-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="a2d46-392">La carga diferida hace que el código de la aplicación sea más fácil de escribir, y con la magia del proxy el código sigue siendo completamente comprobable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="a2d46-393">Las falsificaciones en memoria de la unidad de trabajo pueden simplemente precargar entidades falsas con datos asociados cuando sea necesario durante una prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="a2d46-394">En este punto, centraremos nuestra atención en&lt;la&gt; creación de repositorios mediante IObjectSet T y examinaremos las abstracciones para ocultar todos los signos del marco de persistencia.</span><span class="sxs-lookup"><span data-stu-id="a2d46-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="a2d46-395">Repositorios personalizados</span><span class="sxs-lookup"><span data-stu-id="a2d46-395">Custom Repositories</span></span>

<span data-ttu-id="a2d46-396">Cuando presentamos por primera vez el patrón de diseño de unidad de trabajo en este artículo, proporcionamos un código de ejemplo para el aspecto que podría tener la unidad de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="a2d46-397">Vamos a volver a presentar esta idea original usando el escenario de tarjeta de tiempo de empleado y empleado con el que hemos estado trabajando.</span><span class="sxs-lookup"><span data-stu-id="a2d46-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="a2d46-398">La principal diferencia entre esta unidad de trabajo y la unidad de trabajo que creamos en la última sección es cómo esta&lt;unidad&gt;de trabajo no utiliza ninguna abstracción del marco EF4 (no hay IObjectSet T).</span><span class="sxs-lookup"><span data-stu-id="a2d46-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="a2d46-399">IObjectSet&lt;&gt; T funciona bien como una interfaz de repositorio, pero la API que expone podría no alinearse perfectamente con las necesidades de nuestra aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2d46-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="a2d46-400">En este próximo enfoque representaremos repositorios&lt;mediante&gt; una abstracción IRepository T personalizada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="a2d46-401">Muchos desarrolladores que siguen el diseño basado en pruebas, el diseño&lt;basado&gt; en el comportamiento y el diseño de metodologías basadas en dominios prefieren el enfoque IRepository T por varias razones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="a2d46-402">En primer lugar, la interfaz IRepository&lt;T&gt; representa una capa "anticorrupción".</span><span class="sxs-lookup"><span data-stu-id="a2d46-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="a2d46-403">Como describe Eric Evans en su libro Domain Driven Design, una capa anticorrupción mantiene el código de dominio alejado de las API de infraestructura, como una API de persistencia.</span><span class="sxs-lookup"><span data-stu-id="a2d46-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="a2d46-404">En segundo lugar, los desarrolladores pueden crear métodos en el repositorio que satisfagan las necesidades exactas de una aplicación (como se descubrió al escribir pruebas).</span><span class="sxs-lookup"><span data-stu-id="a2d46-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="a2d46-405">Por ejemplo, es posible que necesitemos con frecuencia buscar una sola entidad mediante un valor ID, por lo que podemos agregar un método FindById a la interfaz del repositorio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="a2d46-406">Nuestra definición de IRepository&lt;T&gt; tendrá el siguiente aspecto.</span><span class="sxs-lookup"><span data-stu-id="a2d46-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="a2d46-407">Tenga en cuenta que volveremos a&lt;&gt; usar una interfaz T IQueryable para exponer colecciones de entidades.</span><span class="sxs-lookup"><span data-stu-id="a2d46-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="a2d46-408">IQueryable&lt;&gt; T permite que los árboles de expresión LINQ fluyan en el proveedor EF4 y proporcionan al proveedor una vista holística de la consulta.</span><span class="sxs-lookup"><span data-stu-id="a2d46-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="a2d46-409">Una segunda opción sería devolver&lt;IEnumerable T&gt;, lo que significa que el proveedor LINQ de EF4 solo verá las expresiones compiladas dentro del repositorio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="a2d46-410">Cualquier agrupación, ordenación y proyección realizada fuera del repositorio no se compondrá en el comando SQL enviado a la base de datos, lo que puede perjudicar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="a2d46-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="a2d46-411">Por otro lado, un repositorio que&lt;devuelve&gt; solo los resultados de IEnumerable T nunca le sorprenderá con un nuevo comando SQL.</span><span class="sxs-lookup"><span data-stu-id="a2d46-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="a2d46-412">Ambos enfoques funcionarán, y ambos enfoques siguen siendo comprobables.</span><span class="sxs-lookup"><span data-stu-id="a2d46-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="a2d46-413">Es sencillo proporcionar una única implementación&lt;de&gt; la interfaz IRepository T mediante genéricos y la API ObjectContext de EF4.</span><span class="sxs-lookup"><span data-stu-id="a2d46-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="a2d46-414">El enfoque&lt;&gt; IRepository T nos da un cierto control adicional sobre nuestras consultas porque un cliente tiene que invocar un método para llegar a una entidad.</span><span class="sxs-lookup"><span data-stu-id="a2d46-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="a2d46-415">Dentro del método podríamos proporcionar comprobaciones adicionales y operadores LINQ para aplicar restricciones de aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2d46-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="a2d46-416">Observe que la interfaz tiene dos restricciones en el parámetro de tipo genérico.</span><span class="sxs-lookup"><span data-stu-id="a2d46-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="a2d46-417">La primera restricción es la clase cons&lt;taint requerida por ObjectSet T&gt;y la segunda restricción obliga a nuestras entidades a implementar IEntity: una abstracción creada para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2d46-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="a2d46-418">La interfaz IEntity obliga a las entidades a tener una propiedad Id legible y, a continuación, podemos usar esta propiedad en el método FindById.</span><span class="sxs-lookup"><span data-stu-id="a2d46-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="a2d46-419">IEntity se define con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="a2d46-420">IEntity podría considerarse una pequeña infracción de la ignorancia de persistencia, ya que nuestras entidades están obligadas a implementar esta interfaz.</span><span class="sxs-lookup"><span data-stu-id="a2d46-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="a2d46-421">Recuerde que la ignorancia de persistencia tiene que ver con las compensaciones y, para muchos, la funcionalidad FindById superará la restricción impuesta por la interfaz.</span><span class="sxs-lookup"><span data-stu-id="a2d46-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="a2d46-422">La interfaz no tiene ningún impacto en la capacidad de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="a2d46-423">La creación de instancias&lt;&gt; de un IRepository T activo requiere un ObjectContext de EF4, por lo que una unidad concreta de implementación de trabajo debe administrar la creación de instancias.</span><span class="sxs-lookup"><span data-stu-id="a2d46-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="a2d46-424">Uso del repositorio personalizado</span><span class="sxs-lookup"><span data-stu-id="a2d46-424">Using the Custom Repository</span></span>

<span data-ttu-id="a2d46-425">El uso de nuestro repositorio personalizado no es significativamente&lt;&gt;diferente del uso del repositorio basado en IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="a2d46-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="a2d46-426">En lugar de aplicar operadores LINQ directamente a una propiedad, primero tendremos que&lt;invocar&gt; uno de los métodos del repositorio para tomar una referencia T IQueryable.</span><span class="sxs-lookup"><span data-stu-id="a2d46-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="a2d46-427">Observe que el operador Include personalizado que implementamos anteriormente funcionará sin cambios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="a2d46-428">El método FindById del repositorio quita la lógica duplicada de las acciones que intentan recuperar una sola entidad.</span><span class="sxs-lookup"><span data-stu-id="a2d46-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="a2d46-429">No hay diferencia significativa en la capacidad de prueba de los dos enfoques que hemos examinado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="a2d46-430">Podríamos proporcionar implementaciones&lt;falsas&gt; de IRepository T mediante&lt;&gt; la creación de clases concretas respaldadas por HashSet Employee - al igual que lo que hicimos en la última sección.</span><span class="sxs-lookup"><span data-stu-id="a2d46-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="a2d46-431">Sin embargo, algunos desarrolladores prefieren usar objetos ficticios y marcos de objetos ficticios en lugar de crear falsificaciones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="a2d46-432">Veremos el uso de simulacros para probar nuestra implementación y discutir las diferencias entre simulacros y falsificaciones en la siguiente sección.</span><span class="sxs-lookup"><span data-stu-id="a2d46-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="a2d46-433">Pruebas con simulacros</span><span class="sxs-lookup"><span data-stu-id="a2d46-433">Testing with Mocks</span></span>

<span data-ttu-id="a2d46-434">Hay diferentes enfoques para construir lo que Martin Fowler llama un "doble de prueba".</span><span class="sxs-lookup"><span data-stu-id="a2d46-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="a2d46-435">Un doble de prueba (como un doble de acrobacias de película) es un objeto que se construye para "permanecer" para objetos reales de producción durante las pruebas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="a2d46-436">Los repositorios en memoria que creamos son duplicaciones de prueba para los repositorios que hablan con SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a2d46-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="a2d46-437">Hemos visto cómo usar estos dobles de prueba durante las pruebas unitarias para aislar el código y mantener las pruebas en ejecución rápidamente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="a2d46-438">Los dobles de prueba que hemos construido tienen implementaciones reales y de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="a2d46-439">En segundo plano, cada uno almacena una colección concreta de objetos, y agregarán y eliminarán objetos de esta colección a medida que manipulamos el repositorio durante una prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="a2d46-440">A algunos desarrolladores les gusta crear sus duplicaciones de prueba de esta manera, con código real e implementaciones de trabajo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="a2d46-441">Estos dobles de prueba son lo que llamamos *falsificaciones.*</span><span class="sxs-lookup"><span data-stu-id="a2d46-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="a2d46-442">Tienen implementaciones de trabajo, pero no son lo suficientemente reales para su uso en producción.</span><span class="sxs-lookup"><span data-stu-id="a2d46-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="a2d46-443">El repositorio falso no escribe realmente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="a2d46-444">El servidor SMTP falso no envía realmente un mensaje de correo electrónico a través de la red.</span><span class="sxs-lookup"><span data-stu-id="a2d46-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="a2d46-445">Mocks versus Fakes</span><span class="sxs-lookup"><span data-stu-id="a2d46-445">Mocks versus Fakes</span></span>

<span data-ttu-id="a2d46-446">Hay otro tipo de doble prueba conocido como *simulado*.</span><span class="sxs-lookup"><span data-stu-id="a2d46-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="a2d46-447">Mientras que las falsificaciones tienen implementaciones de trabajo, los simulacros vienen sin implementación.</span><span class="sxs-lookup"><span data-stu-id="a2d46-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="a2d46-448">Con la ayuda de un marco de objetos ficticios, construimos estos objetos ficticios en tiempo de ejecución y los usamos como dobles de prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="a2d46-449">En esta sección usaremos el marco de simulación de código abierto Moq.</span><span class="sxs-lookup"><span data-stu-id="a2d46-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="a2d46-450">Este es un ejemplo sencillo de uso de Moq para crear dinámicamente un doble de prueba para un repositorio de empleados.</span><span class="sxs-lookup"><span data-stu-id="a2d46-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="a2d46-451">Le pedimos a Moq una implementación de IRepository&lt;Employee&gt; y crea una dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="a2d46-452">Podemos llegar al objeto que&lt;&gt; implementa IRepository Employee accediendo a la propiedad Object del objeto Mock&lt;T.&gt;</span><span class="sxs-lookup"><span data-stu-id="a2d46-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="a2d46-453">Es este objeto interno que podemos pasar a nuestros controladores, y no sabrán si se trata de una prueba doble o el repositorio real.</span><span class="sxs-lookup"><span data-stu-id="a2d46-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="a2d46-454">Podemos invocar métodos en el objeto del mismo lado que invocaríamos métodos en un objeto con una implementación real.</span><span class="sxs-lookup"><span data-stu-id="a2d46-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="a2d46-455">Debe estar preguntándose qué hará el repositorio ficticio cuando invoquemos el método Add.</span><span class="sxs-lookup"><span data-stu-id="a2d46-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="a2d46-456">Puesto que no hay ninguna implementación detrás del objeto ficticio, Add no hace nada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="a2d46-457">No hay una colección concreta detrás de las escenas como la que tuvimos con las falsificaciones que escribimos, por lo que el empleado es descartado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="a2d46-458">¿Qué pasa con el valor devuelto de FindById?</span><span class="sxs-lookup"><span data-stu-id="a2d46-458">What about the return value of FindById?</span></span> <span data-ttu-id="a2d46-459">En este caso, el objeto ficticio hace lo único que puede hacer, que es devolver un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="a2d46-460">Puesto que estamos devolviendo un tipo de referencia (un Employee), el valor devuelto es un valor nulo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="a2d46-461">Las burlas pueden sonar inútiles; sin embargo, hay dos características más de simulacros de los que no hemos hablado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="a2d46-462">En primer lugar, el marco Moq registra todas las llamadas realizadas en el objeto ficticio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="a2d46-463">Más adelante en el código podemos preguntar a Moq si alguien invocó el método Add o si alguien invocó el método FindById.</span><span class="sxs-lookup"><span data-stu-id="a2d46-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="a2d46-464">Veremos más adelante cómo podemos usar esta función de grabación de "caja negra" en las pruebas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="a2d46-465">La segunda gran característica es cómo podemos usar Moq para programar un objeto ficticio con *expectativas.*</span><span class="sxs-lookup"><span data-stu-id="a2d46-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="a2d46-466">Una expectativa indica al objeto ficticio cómo responder a cualquier interacción dada.</span><span class="sxs-lookup"><span data-stu-id="a2d46-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="a2d46-467">Por ejemplo, podemos programar una expectativa en nuestro simulacro y decirle que devuelva un objeto employee cuando alguien invoca FindById.</span><span class="sxs-lookup"><span data-stu-id="a2d46-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="a2d46-468">El marco de trabajo de Moq utiliza una API de instalación y expresiones lambda para programar estas expectativas.</span><span class="sxs-lookup"><span data-stu-id="a2d46-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="a2d46-469">En este ejemplo le pedimos a Moq que cree dinámicamente un repositorio y, a continuación, programamos el repositorio con una expectativa.</span><span class="sxs-lookup"><span data-stu-id="a2d46-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="a2d46-470">La expectativa indica al objeto ficticio que devuelva un nuevo objeto employee con un valor Id de 5 cuando alguien invoca el método FindById pasando un valor de 5.</span><span class="sxs-lookup"><span data-stu-id="a2d46-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="a2d46-471">Esta prueba pasa y no necesitábamos crear una implementación&lt;completa&gt;para falsificar IRepository T .</span><span class="sxs-lookup"><span data-stu-id="a2d46-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="a2d46-472">Repasemos las pruebas que escribimos anteriormente y reelaboremos para usar simulacros en lugar de falsificaciones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="a2d46-473">Al igual que antes, usaremos una clase base para configurar las piezas comunes de infraestructura que necesitamos para todas las pruebas del controlador.</span><span class="sxs-lookup"><span data-stu-id="a2d46-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="a2d46-474">El código de configuración sigue siendo principalmente el mismo.</span><span class="sxs-lookup"><span data-stu-id="a2d46-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="a2d46-475">En lugar de usar falsificaciones, usaremos Moq para construir objetos ficticios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="a2d46-476">La clase base organiza que la unidad de trabajo simulada devuelva un repositorio ficticio cuando el código invoca la propiedad Employees.</span><span class="sxs-lookup"><span data-stu-id="a2d46-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="a2d46-477">El resto de la configuración simulada tendrá lugar dentro de los accesorios de prueba dedicados a cada escenario específico.</span><span class="sxs-lookup"><span data-stu-id="a2d46-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="a2d46-478">Por ejemplo, el accesorio de prueba para la acción Index configurará el repositorio ficticio para devolver una lista de empleados cuando la acción invoque el método FindAll del repositorio ficticio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="a2d46-479">Excepto por las expectativas, nuestras pruebas son similares a las que teníamos antes.</span><span class="sxs-lookup"><span data-stu-id="a2d46-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="a2d46-480">Sin embargo, con la capacidad de grabación de un marco ficticio podemos abordar las pruebas desde un ángulo diferente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="a2d46-481">Veremos esta nueva perspectiva en la siguiente sección.</span><span class="sxs-lookup"><span data-stu-id="a2d46-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="a2d46-482">Pruebas de Estado versus Interacciones</span><span class="sxs-lookup"><span data-stu-id="a2d46-482">State versus Interaction Testing</span></span>

<span data-ttu-id="a2d46-483">Hay diferentes técnicas que puede utilizar para probar software con objetos ficticios.</span><span class="sxs-lookup"><span data-stu-id="a2d46-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="a2d46-484">Un enfoque es utilizar pruebas basadas en el estado, que es lo que hemos hecho en este documento hasta ahora.</span><span class="sxs-lookup"><span data-stu-id="a2d46-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="a2d46-485">Las pruebas basadas en estado hacen afirmaciones sobre el estado del software.</span><span class="sxs-lookup"><span data-stu-id="a2d46-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="a2d46-486">En la última prueba invocamos un método de acción en el controlador y realizamos una aserción sobre el modelo que debe compilar.</span><span class="sxs-lookup"><span data-stu-id="a2d46-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="a2d46-487">Estos son algunos otros ejemplos de estado de prueba:</span><span class="sxs-lookup"><span data-stu-id="a2d46-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="a2d46-488">Compruebe que el repositorio contiene el nuevo objeto employee después de ejecutar Create.</span><span class="sxs-lookup"><span data-stu-id="a2d46-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="a2d46-489">Compruebe que el modelo contiene una lista de todos los empleados después de que se ejecute Index.</span><span class="sxs-lookup"><span data-stu-id="a2d46-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="a2d46-490">Compruebe que el repositorio no contiene un empleado determinado después de ejecutar Delete.</span><span class="sxs-lookup"><span data-stu-id="a2d46-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="a2d46-491">Otro enfoque que verá con objetos ficticios es verificar las *interacciones.*</span><span class="sxs-lookup"><span data-stu-id="a2d46-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="a2d46-492">Mientras que las pruebas basadas en estado hacen aserciones sobre el estado de los objetos, las pruebas basadas en interacciones hacen aserciones sobre cómo interactúan los objetos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="a2d46-493">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a2d46-493">For example:</span></span>

-   <span data-ttu-id="a2d46-494">Compruebe que el controlador invoca el método Add del repositorio cuando Se ejecuta Create.</span><span class="sxs-lookup"><span data-stu-id="a2d46-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="a2d46-495">Compruebe que el controlador invoca el método FindAll del repositorio cuando se ejecuta Index.</span><span class="sxs-lookup"><span data-stu-id="a2d46-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="a2d46-496">Compruebe que el controlador invoca el método Commit de la unidad de trabajo para guardar los cambios cuando se ejecuta Edit.</span><span class="sxs-lookup"><span data-stu-id="a2d46-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="a2d46-497">Las pruebas de interacción a menudo requieren menos datos de prueba, porque no estamos husmeando dentro de las colecciones y verificando los recuentos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="a2d46-498">Por ejemplo, si sabemos que la acción Details invoca el método FindById de un repositorio con el valor correcto, es probable que la acción se comporte correctamente.</span><span class="sxs-lookup"><span data-stu-id="a2d46-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="a2d46-499">Podemos comprobar este comportamiento sin configurar ningún dato de prueba para volver de FindById.</span><span class="sxs-lookup"><span data-stu-id="a2d46-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="a2d46-500">La única configuración requerida en el accesorio de prueba anterior es la configuración proporcionada por la clase base.</span><span class="sxs-lookup"><span data-stu-id="a2d46-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="a2d46-501">Cuando invocamos la acción del controlador, Moq registrará las interacciones con el repositorio ficticio.</span><span class="sxs-lookup"><span data-stu-id="a2d46-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="a2d46-502">Mediante la API Verify de Moq, podemos preguntar a Moq si el controlador invocó FindById con el valor de ID adecuado.</span><span class="sxs-lookup"><span data-stu-id="a2d46-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="a2d46-503">Si el controlador no invocó el método o invocó el método con un valor de parámetro inesperado, el método Verify producirá una excepción y se producirá un error en la prueba.</span><span class="sxs-lookup"><span data-stu-id="a2d46-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="a2d46-504">Este es otro ejemplo para comprobar que la acción Crear invoca Confirmar en la unidad de trabajo actual.</span><span class="sxs-lookup"><span data-stu-id="a2d46-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="a2d46-505">Un peligro con las pruebas de interacción es la tendencia a especificar interacciones en exceso.</span><span class="sxs-lookup"><span data-stu-id="a2d46-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="a2d46-506">La capacidad del objeto ficticio para registrar y comprobar cada interacción con el objeto ficticio no significa que la prueba debe intentar comprobar cada interacción.</span><span class="sxs-lookup"><span data-stu-id="a2d46-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="a2d46-507">Algunas interacciones son detalles de implementación y solo debe comprobar las interacciones *necesarias* para satisfacer la prueba actual.</span><span class="sxs-lookup"><span data-stu-id="a2d46-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="a2d46-508">La elección entre simulacros o falsificaciones depende en gran medida del sistema que esté probando y de sus preferencias personales (o de equipo).</span><span class="sxs-lookup"><span data-stu-id="a2d46-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="a2d46-509">Los objetos ficticios pueden reducir drásticamente la cantidad de código que necesita para implementar dobles de prueba, pero no todo el mundo se siente cómodo programando las expectativas y verificando las interacciones.</span><span class="sxs-lookup"><span data-stu-id="a2d46-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="a2d46-510">Conclusiones</span><span class="sxs-lookup"><span data-stu-id="a2d46-510">Conclusions</span></span>

<span data-ttu-id="a2d46-511">En este artículo hemos demostrado varios enfoques para crear código comprobable mientras se usa el ADO.NET Entity Framework para la persistencia de datos.</span><span class="sxs-lookup"><span data-stu-id="a2d46-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="a2d46-512">Podemos aprovechar las abstracciones integradas como&lt;&gt;IObjectSet T , o crear&lt;nuestras propias abstracciones como IRepository T&gt;.</span><span class="sxs-lookup"><span data-stu-id="a2d46-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="a2d46-513">En ambos casos, la compatibilidad con POCO en la ADO.NET Entity Framework 4.0 permite a los consumidores de estas abstracciones permanecer ignorantes persistentes y altamente comprobables.</span><span class="sxs-lookup"><span data-stu-id="a2d46-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="a2d46-514">Las características adicionales de EF4, como la carga diferida implícita, permiten que el código de servicio empresarial y de aplicaciones funcione sin preocuparse por los detalles de un almacén de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="a2d46-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="a2d46-515">Por último, las abstracciones que creamos son fáciles de simular o falsas dentro de las pruebas unitarias, y podemos usar estos dobles de prueba para lograr pruebas de funcionamiento rápido, altamente aisladas y confiables.</span><span class="sxs-lookup"><span data-stu-id="a2d46-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="a2d46-516">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a2d46-516">Additional Resources</span></span>

-   <span data-ttu-id="a2d46-517">Robert C. Martin, "El principio de [responsabilidad única"](https://www.objectmentor.com/resources/articles/srp.pdf)</span><span class="sxs-lookup"><span data-stu-id="a2d46-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="a2d46-518">Martin Fowler, [Catálogo de patrones](https://www.martinfowler.com/eaaCatalog/index.html) de patrones de arquitectura de aplicaciones *empresariales*</span><span class="sxs-lookup"><span data-stu-id="a2d46-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="a2d46-519">Griffin Caprio, ["Inyección](https://msdn.microsoft.com/magazine/cc163739.aspx)de dependencia"</span><span class="sxs-lookup"><span data-stu-id="a2d46-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="a2d46-520">Blog de programación de datos, " [Tutorial: Desarrollo controlado por pruebas con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".</span><span class="sxs-lookup"><span data-stu-id="a2d46-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="a2d46-521">Blog de programación de datos, " Uso de patrones de [repositorio y unidad de trabajo con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="a2d46-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="a2d46-522">Aaron Jensen, ["Introduciendo las Especificaciones de la Máquina"](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)</span><span class="sxs-lookup"><span data-stu-id="a2d46-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="a2d46-523">Eric Lee, ["BDD con MSTest"](https://saintgimp.org/2009/01/20/bdd-with-mstest/)</span><span class="sxs-lookup"><span data-stu-id="a2d46-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="a2d46-524">Eric Evans, " [Diseño impulsado por dominio](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="a2d46-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="a2d46-525">Martin Fowler, ["Los bofitos no son stubs"](https://martinfowler.com/articles/mocksArentStubs.html)</span><span class="sxs-lookup"><span data-stu-id="a2d46-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="a2d46-526">Martin Fowler, " [Prueba Doble](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="a2d46-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="a2d46-527">Moq</span><span class="sxs-lookup"><span data-stu-id="a2d46-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="a2d46-528">Biografía</span><span class="sxs-lookup"><span data-stu-id="a2d46-528">Biography</span></span>

<span data-ttu-id="a2d46-529">Scott Allen es miembro del personal técnico de Pluralsight y fundador de OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="a2d46-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="a2d46-530">En 15 años de desarrollo de software comercial, Scott ha trabajado en soluciones para todo, desde dispositivos integrados de 8 bits hasta aplicaciones web ASP.NET altamente escalables.</span><span class="sxs-lookup"><span data-stu-id="a2d46-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="a2d46-531">Puede comunicarse con Scott en su blog en [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)OdeToCode, o en Twitter en .</span><span class="sxs-lookup"><span data-stu-id="a2d46-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
