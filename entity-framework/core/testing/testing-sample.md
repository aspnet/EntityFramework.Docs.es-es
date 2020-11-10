---
title: 'Ejemplo de prueba de EF Core: EF Core'
description: Ejemplo que muestra cómo probar las aplicaciones que usan Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- ':::no-loc(Item):::'
- ':::no-loc(Tag):::'
- ':::no-loc(Items):::'
- ':::no-loc(Tags):::'
- ':::no-loc(items):::'
- ':::no-loc(tags):::'
ms.openlocfilehash: 9666bbde8ae9608dcebbea3ad37c51883960a942
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431370"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="4c897-103">Ejemplo de prueba de EF Core</span><span class="sxs-lookup"><span data-stu-id="4c897-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="4c897-104">El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/).</span><span class="sxs-lookup"><span data-stu-id="4c897-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/).</span></span>
> <span data-ttu-id="4c897-105">Tenga en cuenta que se espera que algunas de estas pruebas **produzcan un error**.</span><span class="sxs-lookup"><span data-stu-id="4c897-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="4c897-106">Los motivos para ello se explican a continuación.</span><span class="sxs-lookup"><span data-stu-id="4c897-106">The reasons for this are explained below.</span></span>

<span data-ttu-id="4c897-107">Este documento le guía a través de un ejemplo para probar el código que usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="4c897-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="4c897-108">Aplicación</span><span class="sxs-lookup"><span data-stu-id="4c897-108">The application</span></span>

<span data-ttu-id="4c897-109">El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/) contiene dos proyectos:</span><span class="sxs-lookup"><span data-stu-id="4c897-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/) contains two projects:</span></span>

- <span data-ttu-id="4c897-110">:::no-loc(Items):::WebApi: una [API Web muy sencilla respaldada por ASP.net Core](/aspnet/core/tutorials/first-web-api) con un solo controlador</span><span class="sxs-lookup"><span data-stu-id="4c897-110">:::no-loc(Items):::WebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api) with a single controller</span></span>
- <span data-ttu-id="4c897-111">Pruebas: un proyecto de prueba de [xUnit](https://xunit.net/) para probar el controlador</span><span class="sxs-lookup"><span data-stu-id="4c897-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="4c897-112">El modelo y las reglas de negocios</span><span class="sxs-lookup"><span data-stu-id="4c897-112">The model and business rules</span></span>

<span data-ttu-id="4c897-113">El modelo de respaldo de esta API tiene dos tipos :::no-loc(Items)::: de entidad: y :::no-loc(Tags)::: .</span><span class="sxs-lookup"><span data-stu-id="4c897-113">The model backing this API has two entity types: :::no-loc(Items)::: and :::no-loc(Tags):::.</span></span>

- <span data-ttu-id="4c897-114">:::no-loc(Items)::: tienen un nombre que distingue entre mayúsculas y minúsculas y una colección de :::no-loc(Tags)::: .</span><span class="sxs-lookup"><span data-stu-id="4c897-114">:::no-loc(Items)::: have a case-sensitive name and a collection of :::no-loc(Tags):::.</span></span>
- <span data-ttu-id="4c897-115">Cada :::no-loc(Tag)::: una tiene una etiqueta y un recuento que representa el número de veces que se ha aplicado a :::no-loc(Item)::: .</span><span class="sxs-lookup"><span data-stu-id="4c897-115">Each :::no-loc(Tag)::: has a label and a count representing the number of times it has been applied to the :::no-loc(Item):::.</span></span>
- <span data-ttu-id="4c897-116">Cada :::no-loc(Item)::: una de ellas solo debe tener una :::no-loc(Tag)::: con una etiqueta determinada.</span><span class="sxs-lookup"><span data-stu-id="4c897-116">Each :::no-loc(Item)::: should only have one :::no-loc(Tag)::: with a given label.</span></span>
  - <span data-ttu-id="4c897-117">Si un elemento se etiqueta con la misma etiqueta más de una vez, se incrementa el recuento de la etiqueta existente con esa etiqueta en lugar de crear una nueva etiqueta.</span><span class="sxs-lookup"><span data-stu-id="4c897-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span>
- <span data-ttu-id="4c897-118">La eliminación de :::no-loc(Item)::: debe eliminar todos los asociados :::no-loc(Tags)::: .</span><span class="sxs-lookup"><span data-stu-id="4c897-118">Deleting an :::no-loc(Item)::: should delete all associated :::no-loc(Tags):::.</span></span>

#### <a name="the-no-locitem-entity-type"></a><span data-ttu-id="4c897-119">El :::no-loc(Item)::: tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="4c897-119">The :::no-loc(Item)::: entity type</span></span>

<span data-ttu-id="4c897-120">El `:::no-loc(Item):::` tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="4c897-120">The `:::no-loc(Item):::` entity type:</span></span>

[!code-csharp[:::no-loc(Item):::EntityType](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Item):::.cs?name=:::no-loc(Item):::EntityType)]

<span data-ttu-id="4c897-121">Y su configuración en `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="4c897-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[Configure:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Items):::Context.cs?name=Configure:::no-loc(Item):::)]

<span data-ttu-id="4c897-122">Observe que el tipo de entidad restringe la manera en que se puede usar para reflejar el modelo de dominio y las reglas de negocios.</span><span class="sxs-lookup"><span data-stu-id="4c897-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="4c897-123">En concreto:</span><span class="sxs-lookup"><span data-stu-id="4c897-123">In particular:</span></span>

- <span data-ttu-id="4c897-124">La clave principal se asigna directamente al `_id` campo y no se expone públicamente.</span><span class="sxs-lookup"><span data-stu-id="4c897-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="4c897-125">EF detecta y usa el constructor privado que acepta el valor y el nombre de la clave principal.</span><span class="sxs-lookup"><span data-stu-id="4c897-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="4c897-126">La `Name` propiedad es de solo lectura y se establece solo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="4c897-126">The `Name` property is read-only and set only in the constructor.</span></span>
- <span data-ttu-id="4c897-127">:::no-loc(Tags)::: se exponen como `IReadOnlyList<:::no-loc(Tag):::>` para evitar modificaciones arbitrarias.</span><span class="sxs-lookup"><span data-stu-id="4c897-127">:::no-loc(Tags)::: are exposed as a `IReadOnlyList<:::no-loc(Tag):::>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="4c897-128">EF asocia el `:::no-loc(Tags):::` nombre de la propiedad con el `_:::no-loc(tags):::` campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="4c897-128">EF associates the `:::no-loc(Tags):::` property with the `_:::no-loc(tags):::` backing field by matching their names.</span></span>
  - <span data-ttu-id="4c897-129">El `Add:::no-loc(Tag):::` método toma una etiqueta de etiqueta e implementa la regla de negocios que se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="4c897-129">The `Add:::no-loc(Tag):::` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="4c897-130">Es decir, solo se agrega una etiqueta para las etiquetas nuevas.</span><span class="sxs-lookup"><span data-stu-id="4c897-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="4c897-131">En caso contrario, se incrementa el recuento de una etiqueta existente.</span><span class="sxs-lookup"><span data-stu-id="4c897-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="4c897-132">La `:::no-loc(Tags):::` propiedad de navegación se configura para una relación de varios a uno</span><span class="sxs-lookup"><span data-stu-id="4c897-132">The `:::no-loc(Tags):::` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="4c897-133">No es necesario que una propiedad de navegación de :::no-loc(Tag)::: a :::no-loc(Item)::: , por lo que no se incluye.</span><span class="sxs-lookup"><span data-stu-id="4c897-133">There is no need for a navigation property from :::no-loc(Tag)::: to :::no-loc(Item):::, so it is not included.</span></span>
  - <span data-ttu-id="4c897-134">Además, no :::no-loc(Tag)::: define una propiedad de clave externa.</span><span class="sxs-lookup"><span data-stu-id="4c897-134">Also, :::no-loc(Tag)::: does not define a foreign key property.</span></span>
    <span data-ttu-id="4c897-135">En su lugar, EF creará y administrará una propiedad en el estado de sombra.</span><span class="sxs-lookup"><span data-stu-id="4c897-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-no-loctag-entity-type"></a><span data-ttu-id="4c897-136">El :::no-loc(Tag)::: tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="4c897-136">The :::no-loc(Tag)::: entity type</span></span>

<span data-ttu-id="4c897-137">El `:::no-loc(Tag):::` tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="4c897-137">The `:::no-loc(Tag):::` entity type:</span></span>

[!code-csharp[:::no-loc(Tag):::EntityType](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Tag):::.cs?name=:::no-loc(Tag):::EntityType)]

<span data-ttu-id="4c897-138">Y su configuración en `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="4c897-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[Configure:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Items):::Context.cs?name=Configure:::no-loc(Tag):::)]

<span data-ttu-id="4c897-139">De forma similar a :::no-loc(Item)::: , :::no-loc(Tag)::: oculta su clave principal y hace que la `Label` propiedad sea de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="4c897-139">Similarly to :::no-loc(Item):::, :::no-loc(Tag)::: hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-no-locitemscontroller"></a><span data-ttu-id="4c897-140">El :::no-loc(Items)::: controlador</span><span class="sxs-lookup"><span data-stu-id="4c897-140">The :::no-loc(Items):::Controller</span></span>

<span data-ttu-id="4c897-141">El controlador de API Web es bastante básico.</span><span class="sxs-lookup"><span data-stu-id="4c897-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="4c897-142">Obtiene un `DbContext` del contenedor de inserción de dependencias a través de la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="4c897-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Constructor)]

<span data-ttu-id="4c897-143">Tiene métodos para obtener todos :::no-loc(Items)::: o un :::no-loc(Item)::: con un nombre determinado:</span><span class="sxs-lookup"><span data-stu-id="4c897-143">It has methods to get all :::no-loc(Items)::: or an :::no-loc(Item)::: with a given name:</span></span>

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Get)]

<span data-ttu-id="4c897-144">Tiene un método para agregar un nuevo :::no-loc(Item)::: :</span><span class="sxs-lookup"><span data-stu-id="4c897-144">It has a method to add a new :::no-loc(Item)::::</span></span>

[!code-csharp[Post:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Post:::no-loc(Item):::)]

<span data-ttu-id="4c897-145">Un método para etiquetar un :::no-loc(Item)::: con una etiqueta:</span><span class="sxs-lookup"><span data-stu-id="4c897-145">A method to tag an :::no-loc(Item)::: with a label:</span></span>

[!code-csharp[Post:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Post:::no-loc(Tag):::)]

<span data-ttu-id="4c897-146">Y un método para eliminar un :::no-loc(Item)::: y todos los asociados :::no-loc(Tags)::: :</span><span class="sxs-lookup"><span data-stu-id="4c897-146">And a method to delete an :::no-loc(Item)::: and all associated :::no-loc(Tags)::::</span></span>

[!code-csharp[Delete:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Delete:::no-loc(Item):::)]

<span data-ttu-id="4c897-147">La mayoría de la validación y el control de errores se han quitado para reducir la confusión.</span><span class="sxs-lookup"><span data-stu-id="4c897-147">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="4c897-148">Las pruebas</span><span class="sxs-lookup"><span data-stu-id="4c897-148">The Tests</span></span>

<span data-ttu-id="4c897-149">Las pruebas se organizan para ejecutarse con varias configuraciones de proveedor de base de datos:</span><span class="sxs-lookup"><span data-stu-id="4c897-149">The tests are organized to run with multiple database provider configurations:</span></span>

- <span data-ttu-id="4c897-150">Proveedor de SQL Server, que es el proveedor que usa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4c897-150">The SQL Server provider, which is the provider used by the application</span></span>
- <span data-ttu-id="4c897-151">Proveedor de SQLite</span><span class="sxs-lookup"><span data-stu-id="4c897-151">The SQLite provider</span></span>
- <span data-ttu-id="4c897-152">Proveedor de SQLite que usa bases de datos de SQLite en memoria</span><span class="sxs-lookup"><span data-stu-id="4c897-152">The SQLite provider using in-memory SQLite databases</span></span>
- <span data-ttu-id="4c897-153">El proveedor de base de datos de EF en memoria</span><span class="sxs-lookup"><span data-stu-id="4c897-153">The EF in-memory database provider</span></span>

<span data-ttu-id="4c897-154">Esto se logra colocando todas las pruebas en una clase base y, a continuación, heredando de esta para probar con cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="4c897-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="4c897-155">Tendrá que cambiar el SQL Server cadena de conexión si no está usando LocalDB.</span><span class="sxs-lookup"><span data-stu-id="4c897-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>
> <span data-ttu-id="4c897-156">Consulte [pruebas con SQLite](xref:core/testing/sqlite) para obtener instrucciones sobre el uso de SQLite para realizar pruebas en memoria.</span><span class="sxs-lookup"><span data-stu-id="4c897-156">See [Testing with SQLite](xref:core/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span>

<span data-ttu-id="4c897-157">Se espera que se produzcan errores en las dos pruebas siguientes:</span><span class="sxs-lookup"><span data-stu-id="4c897-157">The following two tests are expected to fail:</span></span>

- <span data-ttu-id="4c897-158">`Can_remove_item_and_all_associated_:::no-loc(tags):::` al ejecutar con el proveedor de base de datos de EF en memoria</span><span class="sxs-lookup"><span data-stu-id="4c897-158">`Can_remove_item_and_all_associated_:::no-loc(tags):::` when running with the EF in-memory database provider</span></span>
- <span data-ttu-id="4c897-159">`Can_add_item_differing_only_by_case` al ejecutar con el proveedor de SQL Server</span><span class="sxs-lookup"><span data-stu-id="4c897-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="4c897-160">Esto se describe con más detalle a continuación.</span><span class="sxs-lookup"><span data-stu-id="4c897-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="4c897-161">Configurar y propagar la base de datos</span><span class="sxs-lookup"><span data-stu-id="4c897-161">Setting up and seeding the database</span></span>

<span data-ttu-id="4c897-162">XUnit, al igual que la mayoría de los marcos de pruebas, creará una nueva instancia de clase de prueba para cada serie de pruebas.</span><span class="sxs-lookup"><span data-stu-id="4c897-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="4c897-163">Además, XUnit no ejecutará en paralelo las pruebas de una clase de prueba determinada.</span><span class="sxs-lookup"><span data-stu-id="4c897-163">Also, XUnit will not run tests within a given test class in parallel.</span></span>
<span data-ttu-id="4c897-164">Esto significa que se puede instalar y configurar la base de datos en el constructor de prueba y que estará en un estado conocido para cada prueba.</span><span class="sxs-lookup"><span data-stu-id="4c897-164">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="4c897-165">En este ejemplo se vuelve a crear la base de datos para cada prueba.</span><span class="sxs-lookup"><span data-stu-id="4c897-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="4c897-166">Esto funciona bien para las pruebas de base de datos en memoria de SQLite y EF, pero puede suponer una sobrecarga significativa con otros sistemas de base de datos, como SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4c897-166">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="4c897-167">Los enfoques para reducir esta sobrecarga se abordan en el [uso compartido de bases de datos entre pruebas](xref:core/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="4c897-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/testing/sharing-databases).</span></span>

<span data-ttu-id="4c897-168">Cuando se ejecuta cada prueba:</span><span class="sxs-lookup"><span data-stu-id="4c897-168">When each test is run:</span></span>

- <span data-ttu-id="4c897-169">DbContextOptions se configuran para el proveedor en uso y se pasan al constructor de clase base</span><span class="sxs-lookup"><span data-stu-id="4c897-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  - <span data-ttu-id="4c897-170">Estas opciones se almacenan en una propiedad y se usan en las pruebas para la creación de instancias de DbContext.</span><span class="sxs-lookup"><span data-stu-id="4c897-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
- <span data-ttu-id="4c897-171">Se llama a un método de inicialización para crear e inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="4c897-171">A Seed method is called to create and seed the database</span></span>
  - <span data-ttu-id="4c897-172">El método de inicialización garantiza que la base de datos está limpia al eliminarla y volver a crearla.</span><span class="sxs-lookup"><span data-stu-id="4c897-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  - <span data-ttu-id="4c897-173">Algunas entidades de prueba conocidas se crean y se guardan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4c897-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=Seeding)]

<span data-ttu-id="4c897-174">Cada clase de prueba concreta hereda de este.</span><span class="sxs-lookup"><span data-stu-id="4c897-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="4c897-175">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4c897-175">For example:</span></span>

[!code-csharp[Sqlite:::no-loc(Items):::ControllerTest](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/Sqlite:::no-loc(Items):::ControllerTest.cs?name=Sqlite:::no-loc(Items):::ControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="4c897-176">Estructura de prueba</span><span class="sxs-lookup"><span data-stu-id="4c897-176">Test structure</span></span>

<span data-ttu-id="4c897-177">Aunque la aplicación usa la inserción de dependencias, las pruebas no lo hacen.</span><span class="sxs-lookup"><span data-stu-id="4c897-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="4c897-178">Sería adecuado usar la inserción de dependencias aquí, pero el código adicional que requiere tiene poco valor.</span><span class="sxs-lookup"><span data-stu-id="4c897-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="4c897-179">En su lugar, se crea un DbContext usando `new` y, a continuación, se pasa directamente como la dependencia al controlador.</span><span class="sxs-lookup"><span data-stu-id="4c897-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="4c897-180">Después, cada prueba ejecuta el método sometido a prueba en el controlador y valida que los resultados son los esperados.</span><span class="sxs-lookup"><span data-stu-id="4c897-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="4c897-181">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4c897-181">For example:</span></span>

[!code-csharp[CanGet:::no-loc(Items):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanGet:::no-loc(Items):::)]

<span data-ttu-id="4c897-182">Observe que se usan diferentes instancias de DbContext para inicializar la base de datos y ejecutar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="4c897-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span>
<span data-ttu-id="4c897-183">Esto garantiza que la prueba no esté usando (o pasando por) entidades cuyo seguimiento realiza el contexto al realizar la propagación.</span><span class="sxs-lookup"><span data-stu-id="4c897-183">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="4c897-184">También mejor coincide con lo que ocurre en servicios y aplicaciones Web.</span><span class="sxs-lookup"><span data-stu-id="4c897-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="4c897-185">Las pruebas que mutan la base de datos crean una segunda instancia de DbContext en la prueba por motivos similares.</span><span class="sxs-lookup"><span data-stu-id="4c897-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="4c897-186">Es decir, crear un nuevo contexto, limpiar y, a continuación, leerlo desde la base de datos para asegurarse de que los cambios se guardaron en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4c897-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span>
<span data-ttu-id="4c897-187">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="4c897-187">For example:</span></span>

[!code-csharp[CanAdd:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Item):::)]

<span data-ttu-id="4c897-188">Dos pruebas ligeramente más complicadas cubren la lógica de negocios en torno a la adición :::no-loc(tags)::: .</span><span class="sxs-lookup"><span data-stu-id="4c897-188">Two slightly more involved tests cover the business logic around adding :::no-loc(tags):::.</span></span>

[!code-csharp[CanAdd:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Tag):::)]

[!code-csharp[CanUp:::no-loc(Tag):::Count](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanUp:::no-loc(Tag):::Count)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="4c897-189">Problemas con diferentes proveedores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="4c897-189">Issues using different database providers</span></span>

<span data-ttu-id="4c897-190">La prueba con un sistema de base de datos diferente al que se usa en la aplicación de producción puede provocar problemas.</span><span class="sxs-lookup"><span data-stu-id="4c897-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="4c897-191">Estos se describen en el nivel conceptual del [código de prueba que usa EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="4c897-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/testing/index).</span></span>
<span data-ttu-id="4c897-192">En las secciones siguientes se incluyen dos ejemplos de estos problemas que se muestran en las pruebas de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="4c897-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="4c897-193">La prueba se supera cuando se interrumpe la aplicación</span><span class="sxs-lookup"><span data-stu-id="4c897-193">Test passes when the application is broken</span></span>

<span data-ttu-id="4c897-194">Uno de los requisitos de nuestra aplicación es que " :::no-loc(Items)::: tiene un nombre que distingue entre mayúsculas y minúsculas y una colección de :::no-loc(Tags)::: ".</span><span class="sxs-lookup"><span data-stu-id="4c897-194">One of the requirements for our application is that ":::no-loc(Items)::: have a case-sensitive name and a collection of :::no-loc(Tags):::."</span></span>
<span data-ttu-id="4c897-195">Esto es bastante sencillo de probar:</span><span class="sxs-lookup"><span data-stu-id="4c897-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAdd:::no-loc(Item):::CaseInsensitive](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Item):::CaseInsensitive)]

<span data-ttu-id="4c897-196">La ejecución de esta prueba en la base de datos en memoria de EF indica que todo está bien.</span><span class="sxs-lookup"><span data-stu-id="4c897-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="4c897-197">Todo sigue teniendo el aspecto correcto al usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="4c897-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="4c897-198">Pero se produce un error en la prueba cuando se ejecuta en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4c897-198">But the test fails when run against SQL Server!</span></span>

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.:::no-loc(Items):::ControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="4c897-199">Esto se debe a que la base de datos de EF en memoria y la base de datos de SQLite distinguen mayúsculas de minúsculas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4c897-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="4c897-200">SQL Server, por otro lado, no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4c897-200">SQL Server, on the other hand, is case-insensitive!</span></span>

<span data-ttu-id="4c897-201">EF Core, por diseño, no cambia estos comportamientos porque forzar un cambio en la distinción de mayúsculas y minúsculas puede tener un gran impacto en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="4c897-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="4c897-202">Una vez que sabemos que se trata de un problema, podemos corregir la aplicación y compensar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="4c897-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="4c897-203">Sin embargo, el punto aquí es que este error podría perderse si solo se prueba con la base de datos de EF en memoria o con proveedores de SQLite.</span><span class="sxs-lookup"><span data-stu-id="4c897-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="4c897-204">Se produce un error en la prueba cuando la aplicación es correcta</span><span class="sxs-lookup"><span data-stu-id="4c897-204">Test fails when the application is correct</span></span>

<span data-ttu-id="4c897-205">Otro de los requisitos para nuestra aplicación es que "la eliminación de un :::no-loc(Item)::: debe eliminar todos los asociados :::no-loc(Tags)::: ".</span><span class="sxs-lookup"><span data-stu-id="4c897-205">Another of the requirements for our application is that "deleting an :::no-loc(Item)::: should delete all associated :::no-loc(Tags):::."</span></span>
<span data-ttu-id="4c897-206">De nuevo, fácil de probar:</span><span class="sxs-lookup"><span data-stu-id="4c897-206">Again, easy to test:</span></span>

[!code-csharp[Delete:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=Delete:::no-loc(Item):::)]

<span data-ttu-id="4c897-207">Esta prueba se supera en SQL Server y SQLite, pero produce un error con la base de datos de EF en memoria.</span><span class="sxs-lookup"><span data-stu-id="4c897-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.:::no-loc(Items):::ControllerTest.Can_remove_item_and_all_associated_:::no-loc(tags):::()
```

<span data-ttu-id="4c897-208">En este caso, la aplicación funciona correctamente porque SQL Server admite [eliminaciones en cascada](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="4c897-208">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span>
<span data-ttu-id="4c897-209">SQLite también admite eliminaciones en cascada, al igual que la mayoría de las bases de datos relacionales, por lo que la prueba en SQLite funciona.</span><span class="sxs-lookup"><span data-stu-id="4c897-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="4c897-210">Por otro lado, la base de datos en memoria de EF [no admite eliminaciones en cascada](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="4c897-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="4c897-211">Esto significa que esta parte de la aplicación no se puede probar con el proveedor de base de datos de EF en memoria.</span><span class="sxs-lookup"><span data-stu-id="4c897-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
