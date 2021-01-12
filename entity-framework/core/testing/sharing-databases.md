---
title: Compartir bases de datos entre pruebas-EF Core
description: Ejemplo que muestra cómo compartir una base de datos entre varias pruebas
author: ajcvickers
ms.date: 04/25/2020
uid: core/testing/sharing-databases
ms.openlocfilehash: 7a90a144271d5c34e9d5043aa439f84db805c6af
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128841"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="1186c-103">Compartir bases de datos entre pruebas</span><span class="sxs-lookup"><span data-stu-id="1186c-103">Sharing databases between tests</span></span>

<span data-ttu-id="1186c-104">En el [ejemplo de pruebas de EF Core](xref:core/testing/testing-sample) se ha mostrado cómo probar aplicaciones en diferentes sistemas de base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-104">The [EF Core testing sample](xref:core/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="1186c-105">Para ese ejemplo, cada prueba ha creado una nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="1186c-106">Este es un buen patrón al usar SQLite o la base de datos en memoria de EF, pero puede suponer una sobrecarga importante al usar otros sistemas de base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="1186c-107">Este ejemplo se basa en el ejemplo anterior moviendo la creación de la base de datos a un accesorio de prueba.</span><span class="sxs-lookup"><span data-stu-id="1186c-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="1186c-108">Esto permite que una sola base de datos de SQL Server se cree y se inicialice solo una vez para todas las pruebas.</span><span class="sxs-lookup"><span data-stu-id="1186c-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="1186c-109">Asegúrese de trabajar en el [ejemplo de prueba de EF Core](xref:core/testing/testing-sample) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="1186c-109">Make sure to work through the [EF Core testing sample](xref:core/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="1186c-110">No es difícil escribir varias pruebas en la misma base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="1186c-111">El truco lo está haciendo de manera que las pruebas no viajen entre sí mientras se ejecutan.</span><span class="sxs-lookup"><span data-stu-id="1186c-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="1186c-112">Esto requiere comprender lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1186c-112">This requires understanding:</span></span>

* <span data-ttu-id="1186c-113">Cómo compartir de forma segura objetos entre pruebas</span><span class="sxs-lookup"><span data-stu-id="1186c-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="1186c-114">Cuando el marco de pruebas ejecuta pruebas en paralelo</span><span class="sxs-lookup"><span data-stu-id="1186c-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="1186c-115">Cómo mantener la base de datos en un estado limpio para cada prueba</span><span class="sxs-lookup"><span data-stu-id="1186c-115">How to keep the database in a clean state for every test</span></span>

## <a name="the-fixture"></a><span data-ttu-id="1186c-116">El accesorio</span><span class="sxs-lookup"><span data-stu-id="1186c-116">The fixture</span></span>

<span data-ttu-id="1186c-117">Usaremos un accesorio de prueba para compartir objetos entre pruebas.</span><span class="sxs-lookup"><span data-stu-id="1186c-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="1186c-118">La [documentación de xUnit](https://xunit.net/docs/shared-context.html) indica que se debe usar un accesorio "Si desea crear un único contexto de prueba y compartirlo entre todas las pruebas de la clase y hacer que se limpie una vez finalizadas todas las pruebas de la clase".</span><span class="sxs-lookup"><span data-stu-id="1186c-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="1186c-119">En este ejemplo se usa [xUnit](https://xunit.net/), pero existen conceptos similares en otros marcos de pruebas, incluido [NUnit](https://nunit.org/).</span><span class="sxs-lookup"><span data-stu-id="1186c-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="1186c-120">Esto significa que se debe trasladar la creación y propagación de la base de datos a una clase de accesorio.</span><span class="sxs-lookup"><span data-stu-id="1186c-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="1186c-121">Este es su aspecto:</span><span class="sxs-lookup"><span data-stu-id="1186c-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="1186c-122">Por ahora, observe cómo el constructor:</span><span class="sxs-lookup"><span data-stu-id="1186c-122">For now, notice how the constructor:</span></span>

* <span data-ttu-id="1186c-123">Crea una conexión de base de datos única para la duración del accesorio.</span><span class="sxs-lookup"><span data-stu-id="1186c-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="1186c-124">Crea y inicializa la base de datos llamando al `Seed` método.</span><span class="sxs-lookup"><span data-stu-id="1186c-124">Creates and seeds that database by calling the `Seed` method</span></span>

<span data-ttu-id="1186c-125">Omitir el bloqueo por ahora; volveremos a él más adelante.</span><span class="sxs-lookup"><span data-stu-id="1186c-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="1186c-126">No es necesario que el código de creación y propagación sea asincrónico.</span><span class="sxs-lookup"><span data-stu-id="1186c-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="1186c-127">Si se convierte en Async, se complicará el código y no se mejorará el rendimiento ni el rendimiento de las pruebas.</span><span class="sxs-lookup"><span data-stu-id="1186c-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="1186c-128">La base de datos se crea mediante la eliminación de cualquier base de datos existente y, a continuación, la creación de una nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="1186c-129">Esto garantiza que la base de datos coincida con el modelo EF actual incluso si se ha cambiado desde la última ejecución de pruebas.</span><span class="sxs-lookup"><span data-stu-id="1186c-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="1186c-130">Puede ser más rápido "limpiar" la base de datos existente usando algo como [regenerar](https://jimmybogard.com/tag/respawn/) en lugar de volver a crearla cada vez.</span><span class="sxs-lookup"><span data-stu-id="1186c-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="1186c-131">Sin embargo, debe tener cuidado para asegurarse de que el esquema de la base de datos esté actualizado con el modelo de EF al hacerlo.</span><span class="sxs-lookup"><span data-stu-id="1186c-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="1186c-132">La conexión a la base de datos se elimina cuando se desecha el accesorio.</span><span class="sxs-lookup"><span data-stu-id="1186c-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="1186c-133">También puede considerar la posibilidad de eliminar la base de datos de prueba en este momento.</span><span class="sxs-lookup"><span data-stu-id="1186c-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="1186c-134">Sin embargo, esto requerirá un recuento de referencias y bloqueo adicional si el accesorio se comparte con varias clases de prueba.</span><span class="sxs-lookup"><span data-stu-id="1186c-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="1186c-135">Además, a menudo resulta útil tener la base de datos de prueba todavía disponible para la depuración de pruebas con errores.</span><span class="sxs-lookup"><span data-stu-id="1186c-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>

## <a name="using-the-fixture"></a><span data-ttu-id="1186c-136">Uso del accesorio</span><span class="sxs-lookup"><span data-stu-id="1186c-136">Using the fixture</span></span>

<span data-ttu-id="1186c-137">XUnit tiene un patrón común para asociar un accesorio de prueba con una clase de pruebas:</span><span class="sxs-lookup"><span data-stu-id="1186c-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="1186c-138">XUnit creará ahora una instancia de accesorio única y la pasará a cada instancia de la clase de prueba.</span><span class="sxs-lookup"><span data-stu-id="1186c-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="1186c-139">(Recuerde en el primer [ejemplo de prueba](xref:core/testing/testing-sample) que xUnit crea una nueva instancia de clase de prueba cada vez que ejecuta una prueba). Esto significa que la base de datos se creará y se inicializará una vez y cada prueba utilizará esta base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-139">(Remember from the first [testing sample](xref:core/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="1186c-140">Tenga en cuenta que las pruebas dentro de una sola clase no se ejecutarán en paralelo.</span><span class="sxs-lookup"><span data-stu-id="1186c-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="1186c-141">Esto significa que es seguro que cada prueba use la misma conexión de base de datos, aunque el `DbConnection` objeto no sea seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="1186c-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="1186c-142">Mantenimiento de estado de base de datos</span><span class="sxs-lookup"><span data-stu-id="1186c-142">Maintaining database state</span></span>

<span data-ttu-id="1186c-143">Las pruebas a menudo necesitan mutar los datos de prueba con inserciones, actualizaciones y eliminaciones.</span><span class="sxs-lookup"><span data-stu-id="1186c-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="1186c-144">Sin embargo, estos cambios afectarán a otras pruebas que esperan una base de datos limpia y propagada.</span><span class="sxs-lookup"><span data-stu-id="1186c-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="1186c-145">Esto se puede solucionar mediante la ejecución de pruebas mutadas dentro de una transacción.</span><span class="sxs-lookup"><span data-stu-id="1186c-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="1186c-146">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1186c-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="1186c-147">Observe que la transacción se crea cuando la prueba se inicia y se desecha cuando finaliza.</span><span class="sxs-lookup"><span data-stu-id="1186c-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="1186c-148">Al desechar la transacción, ésta se revierte, por lo que ninguna otra prueba verá ninguno de los cambios.</span><span class="sxs-lookup"><span data-stu-id="1186c-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="1186c-149">El método auxiliar para crear un contexto (consulte el código de accesorio anterior) acepta esta transacción y opta por que el DbContext la use.</span><span class="sxs-lookup"><span data-stu-id="1186c-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span>

## <a name="sharing-the-fixture"></a><span data-ttu-id="1186c-150">Uso compartido del accesorio</span><span class="sxs-lookup"><span data-stu-id="1186c-150">Sharing the fixture</span></span>

<span data-ttu-id="1186c-151">Es posible que haya observado el bloqueo del código en torno a la creación y propagación de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="1186c-152">Esto no es necesario para este ejemplo, ya que solo una clase de pruebas usa el accesorio, por lo que solo se crea una instancia de accesorio.</span><span class="sxs-lookup"><span data-stu-id="1186c-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="1186c-153">Sin embargo, puede que desee usar el mismo accesorio con varias clases de pruebas.</span><span class="sxs-lookup"><span data-stu-id="1186c-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="1186c-154">XUnit creará una instancia de accesorio para cada una de estas clases.</span><span class="sxs-lookup"><span data-stu-id="1186c-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="1186c-155">Estos pueden ser usados por diferentes subprocesos que ejecutan pruebas en paralelo.</span><span class="sxs-lookup"><span data-stu-id="1186c-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="1186c-156">Por lo tanto, es importante tener un bloqueo adecuado para asegurarse de que solo un subproceso realiza la creación y propagación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1186c-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="1186c-157">`lock`Aquí está bien un sencillo.</span><span class="sxs-lookup"><span data-stu-id="1186c-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="1186c-158">No es necesario intentar nada más complejo, como los patrones sin bloqueos.</span><span class="sxs-lookup"><span data-stu-id="1186c-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
