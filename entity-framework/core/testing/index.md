---
title: 'Pruebas de código que usa EF Core: EF Core'
description: Diferentes métodos para probar aplicaciones que usan Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/index
ms.openlocfilehash: 5ac7954a87697e43b68845e925e2510c027b7135
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543580"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="577bc-103">Pruebas de código que usa EF Core</span><span class="sxs-lookup"><span data-stu-id="577bc-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="577bc-104">Para probar el código que accede a una base de datos, es necesario:</span><span class="sxs-lookup"><span data-stu-id="577bc-104">Testing code that accesses a database requires either:</span></span>

* <span data-ttu-id="577bc-105">Ejecutar consultas y actualizaciones en el mismo sistema de base de datos que se usa en producción.</span><span class="sxs-lookup"><span data-stu-id="577bc-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="577bc-106">Ejecutar consultas y actualizaciones en algún otro sistema de base de datos más fácil de administrar.</span><span class="sxs-lookup"><span data-stu-id="577bc-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="577bc-107">Usar dobles de prueba o algún otro mecanismo para evitar por completo el uso de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="577bc-108">En este documento se describen las ventajas y los inconvenientes de cada una de estas opciones, y se muestra cómo usar EF Core con cada método.</span><span class="sxs-lookup"><span data-stu-id="577bc-108">This document outlines the trade-offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>

> [!TIP]
> <span data-ttu-id="577bc-109">Eche un vistazo al [ejemplo de prueba de EF Core](xref:core/testing/testing-sample) para ver código que muestra los conceptos descritos aquí.</span><span class="sxs-lookup"><span data-stu-id="577bc-109">See [EF Core testing sample](xref:core/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span>

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="577bc-110">Todos los proveedores de bases de datos no son iguales</span><span class="sxs-lookup"><span data-stu-id="577bc-110">All database providers are not equal</span></span>

<span data-ttu-id="577bc-111">Es muy importante entender que EF Core no está diseñado para extraer cada aspecto del sistema de base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="577bc-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="577bc-112">EF Core es un conjunto común de patrones y conceptos que se pueden usar con cualquier sistema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="577bc-113">Así, los proveedores de bases de datos de EF Core basan el comportamiento y la funcionalidad específicos de base de datos en este marco común.</span><span class="sxs-lookup"><span data-stu-id="577bc-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="577bc-114">Esto permite a cada sistema de base de datos hacer lo que mejor se le da, a la vez que mantiene la homogeneidad, si fuera necesario, con otros sistemas de base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span>

<span data-ttu-id="577bc-115">Básicamente, esto significa que, al cambiar de proveedor de base de datos, cambia el comportamiento de EF Core y no se puede esperar que la aplicación funcione correctamente, a menos que tenga en cuenta de forma explícita las diferencias de comportamiento.</span><span class="sxs-lookup"><span data-stu-id="577bc-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="577bc-116">Dicho esto, en muchos casos esto funciona, ya que hay un alto grado de homogeneidad entre bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="577bc-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="577bc-117">Esto es bueno y malo.</span><span class="sxs-lookup"><span data-stu-id="577bc-117">This is good and bad.</span></span>
<span data-ttu-id="577bc-118">Es bueno porque el cambio entre sistemas de bases de datos puede ser relativamente fácil.</span><span class="sxs-lookup"><span data-stu-id="577bc-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="577bc-119">Es malo porque puede dar una falsa sensación de seguridad si la aplicación no se prueba por completo en el nuevo sistema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>

## <a name="approach-1-production-database-system"></a><span data-ttu-id="577bc-120">Enfoque 1: Sistema de base de datos de producción</span><span class="sxs-lookup"><span data-stu-id="577bc-120">Approach 1: Production database system</span></span>

<span data-ttu-id="577bc-121">Como se ha explicado en la sección anterior, la única manera de asegurarse de que se está probando lo que se ejecuta en producción es usar el mismo sistema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="577bc-122">Por ejemplo, si la aplicación implementada usa SQL Azure, las pruebas también deben realizarse en SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="577bc-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="577bc-123">Pero que cada desarrollador ejecute pruebas en SQL Azure mientras trabaja activamente en el código es lento y costoso.</span><span class="sxs-lookup"><span data-stu-id="577bc-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="577bc-124">Esto muestra la principal contrapartida de estos métodos: ¿cuándo resulta adecuado desviarse del sistema de base de datos de producción para mejorar la eficacia de las pruebas?</span><span class="sxs-lookup"><span data-stu-id="577bc-124">This illustrates the main trade-off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="577bc-125">Afortunadamente, en este caso, la respuesta es bastante fácil: use la instancia local de SQL Server para las pruebas de desarrollador.</span><span class="sxs-lookup"><span data-stu-id="577bc-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="577bc-126">SQL Azure y SQL Server son muy similares, por lo que realizar las pruebas en SQL Server suele ser una contrapartida razonable.</span><span class="sxs-lookup"><span data-stu-id="577bc-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade-off.</span></span>
<span data-ttu-id="577bc-127">Dicho esto, sigue siendo aconsejable ejecutar las pruebas en SQL Azure antes de pasar a producción.</span><span class="sxs-lookup"><span data-stu-id="577bc-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>

### <a name="localdb"></a><span data-ttu-id="577bc-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="577bc-128">LocalDB</span></span>

<span data-ttu-id="577bc-129">Todos los principales sistemas de base de datos tienen alguna forma de "edición para desarrolladores" para las pruebas locales.</span><span class="sxs-lookup"><span data-stu-id="577bc-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="577bc-130">SQL Server también tiene una característica denominada [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="577bc-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).</span></span>
<span data-ttu-id="577bc-131">La principal ventaja de LocalDB es que inicia la instancia de base de datos a petición.</span><span class="sxs-lookup"><span data-stu-id="577bc-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="577bc-132">Esto evita que haya un servicio de base de datos ejecutándose en el equipo aunque no se estén ejecutando pruebas.</span><span class="sxs-lookup"><span data-stu-id="577bc-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="577bc-133">LocalDB también presenta algunos inconvenientes:</span><span class="sxs-lookup"><span data-stu-id="577bc-133">LocalDB is not without its issues:</span></span>

* <span data-ttu-id="577bc-134">No admite todo lo que [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="577bc-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) does.</span></span>
* <span data-ttu-id="577bc-135">No está disponible en Linux.</span><span class="sxs-lookup"><span data-stu-id="577bc-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="577bc-136">Puede producir un retraso en la primera serie de pruebas cuando se inicia el servicio.</span><span class="sxs-lookup"><span data-stu-id="577bc-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="577bc-137">Personalmente, nunca me ha parecido un problema que haya un servicio de base de datos ejecutándose en el equipo de desarrollo y, en general, recomendaría usar Developer Edition.</span><span class="sxs-lookup"><span data-stu-id="577bc-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="577bc-138">Con todo, LocalDB puede ser adecuado para algunas personas, especialmente en equipos de desarrollo menos potentes.</span><span class="sxs-lookup"><span data-stu-id="577bc-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="577bc-139">Otra manera de evitar que el sistema de base de datos se ejecute directamente en el equipo de desarrollo es [ejecutar SQL Server](/sql/linux/quickstart-install-connect-docker) (o cualquier otro sistema de base de datos) en un contenedor de Docker (o similar).</span><span class="sxs-lookup"><span data-stu-id="577bc-139">[Running SQL Server](/sql/linux/quickstart-install-connect-docker) (or any other database system) in a Docker container (or similar) is another way to avoid running the database system directly on your development machine.</span></span>

## <a name="approach-2-sqlite"></a><span data-ttu-id="577bc-140">Enfoque 2: SQLite</span><span class="sxs-lookup"><span data-stu-id="577bc-140">Approach 2: SQLite</span></span>

<span data-ttu-id="577bc-141">EF Core prueba el proveedor de SQL Server principalmente mediante su ejecución en una instancia local de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="577bc-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="577bc-142">Estas pruebas ejecutan decenas de miles de consultas en un par de minutos en un equipo rápido.</span><span class="sxs-lookup"><span data-stu-id="577bc-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="577bc-143">Esto muestra que el uso del sistema de base de datos real puede ser una solución eficiente.</span><span class="sxs-lookup"><span data-stu-id="577bc-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="577bc-144">Es un mito que el uso de una base de datos más ligera sea la única manera de ejecutar pruebas rápidamente.</span><span class="sxs-lookup"><span data-stu-id="577bc-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="577bc-145">Dicho esto, ¿qué ocurre si, por cualquier motivo, no se pueden ejecutar pruebas en algo cercano al sistema de base de datos de producción?</span><span class="sxs-lookup"><span data-stu-id="577bc-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="577bc-146">La siguiente mejor opción es usar algo con funcionalidad similar.</span><span class="sxs-lookup"><span data-stu-id="577bc-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="577bc-147">Esto suele significar otra base de datos relacional, para lo que [SQLite](https://sqlite.org/index.html) es la opción obvia.</span><span class="sxs-lookup"><span data-stu-id="577bc-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="577bc-148">SQLite es una buena opción porque:</span><span class="sxs-lookup"><span data-stu-id="577bc-148">SQLite is a good choice because:</span></span>

* <span data-ttu-id="577bc-149">Se ejecuta en proceso con la aplicación y, por tanto, tiene poca sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="577bc-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="577bc-150">Usa archivos simples creados automáticamente para bases de datos, por lo que no requiere administración de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="577bc-151">Tiene un modo en memoria que evita incluso la creación de archivos.</span><span class="sxs-lookup"><span data-stu-id="577bc-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="577bc-152">Pero recuerde que:</span><span class="sxs-lookup"><span data-stu-id="577bc-152">However, remember that:</span></span>

* <span data-ttu-id="577bc-153">SQLite inevitablemente no admite todo lo que el sistema de base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="577bc-153">SQLite inevitably doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="577bc-154">SQLite se comporta de forma diferente al sistema de base de datos de producción para algunas consultas.</span><span class="sxs-lookup"><span data-stu-id="577bc-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="577bc-155">Por lo tanto, si usa SQLite para algunas pruebas, asegúrese de probar también en el sistema de base de datos real.</span><span class="sxs-lookup"><span data-stu-id="577bc-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="577bc-156">Vea [Pruebas con SQLite](xref:core/testing/sqlite) para obtener instrucciones específicas de EF Core.</span><span class="sxs-lookup"><span data-stu-id="577bc-156">See [Testing with SQLite](xref:core/testing/sqlite) for EF Core specific guidance.</span></span>

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="577bc-157">Método 3: Base de datos en memoria de EF Core</span><span class="sxs-lookup"><span data-stu-id="577bc-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="577bc-158">EF Core incluye una base de datos en memoria que se usa para las pruebas internas del propio EF Core.</span><span class="sxs-lookup"><span data-stu-id="577bc-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="577bc-159">Esta base de datos en general **no es adecuada para probar las aplicaciones que usan EF Core**.</span><span class="sxs-lookup"><span data-stu-id="577bc-159">This database is in general **not suitable for testing applications that use EF Core**.</span></span> <span data-ttu-id="577bc-160">De manera específica:</span><span class="sxs-lookup"><span data-stu-id="577bc-160">Specifically:</span></span>

* <span data-ttu-id="577bc-161">No es una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="577bc-161">It is not a relational database.</span></span>
* <span data-ttu-id="577bc-162">No admite transacciones.</span><span class="sxs-lookup"><span data-stu-id="577bc-162">It doesn't support transactions.</span></span>
* <span data-ttu-id="577bc-163">No puede ejecutar consultas SQL sin formato.</span><span class="sxs-lookup"><span data-stu-id="577bc-163">It cannot run raw SQL queries.</span></span>
* <span data-ttu-id="577bc-164">No está optimizada para el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="577bc-164">It is not optimized for performance.</span></span>

<span data-ttu-id="577bc-165">Nada de esto es muy importante a la hora de probar elementos internos de EF Core, ya que se usa específicamente donde la base de datos es irrelevante para la prueba.</span><span class="sxs-lookup"><span data-stu-id="577bc-165">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="577bc-166">Por otro lado, estos aspectos tienden a ser muy importantes al probar una aplicación que usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="577bc-166">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="577bc-167">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="577bc-167">Unit testing</span></span>

<span data-ttu-id="577bc-168">Imagine que va a probar una parte de la lógica de negocio que pueda necesitar usar algunos datos de una base de datos, pero que no supone probar propiamente las interacciones de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-168">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="577bc-169">Una opción es usar un [doble de prueba](https://en.wikipedia.org/wiki/Test_double) como simulacro o imitación.</span><span class="sxs-lookup"><span data-stu-id="577bc-169">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="577bc-170">Los dobles de prueba se usan para las pruebas internas de EF Core.</span><span class="sxs-lookup"><span data-stu-id="577bc-170">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="577bc-171">Pero nunca se intentan simular DbContext o IQueryable.</span><span class="sxs-lookup"><span data-stu-id="577bc-171">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="577bc-172">Hacerlo es difícil, engorroso y delicado.</span><span class="sxs-lookup"><span data-stu-id="577bc-172">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="577bc-173">**No lo haga.**</span><span class="sxs-lookup"><span data-stu-id="577bc-173">**Don't do it.**</span></span>

<span data-ttu-id="577bc-174">En su lugar, se usa la base de datos en memoria de EF siempre que se realizan pruebas unitarias de algo que use DbContext.</span><span class="sxs-lookup"><span data-stu-id="577bc-174">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="577bc-175">En este caso, el uso de la base de datos en memoria de EF es adecuado porque la prueba no depende del comportamiento de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-175">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="577bc-176">Pero no lo haga para probar consultas o actualizaciones reales de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="577bc-176">Just don't do this to test actual database queries or updates.</span></span>

<span data-ttu-id="577bc-177">En el [ejemplo de prueba de EF Core](xref:core/testing/testing-sample) puede ver pruebas que usan la base de datos en memoria de EF, así como SQL Server y SQLite.</span><span class="sxs-lookup"><span data-stu-id="577bc-177">The [EF Core testing sample](xref:core/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span>
