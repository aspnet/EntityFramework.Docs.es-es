---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672820"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="fc6ca-101">Ejemplo de prueba de EF Core</span><span class="sxs-lookup"><span data-stu-id="fc6ca-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="fc6ca-102">El código de este documento se puede encontrar en GitHub como un [ejemplo ejecutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="fc6ca-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="fc6ca-103">Tenga en cuenta que se espera que algunas de estas pruebas **produzcan un error**.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="fc6ca-104">Los motivos para ello se explican a continuación.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="fc6ca-105">Este documento le guía a través de un ejemplo para probar el código que usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="fc6ca-106">Aplicación</span><span class="sxs-lookup"><span data-stu-id="fc6ca-106">The application</span></span>

<span data-ttu-id="fc6ca-107">El [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contiene dos proyectos:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="fc6ca-108">ItemsWebApi: una [API Web muy sencilla respaldada por ASP.net Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) con un solo controlador</span><span class="sxs-lookup"><span data-stu-id="fc6ca-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="fc6ca-109">Pruebas: un proyecto de prueba de [xUnit](https://xunit.net/) para probar el controlador</span><span class="sxs-lookup"><span data-stu-id="fc6ca-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="fc6ca-110">El modelo y las reglas de negocios</span><span class="sxs-lookup"><span data-stu-id="fc6ca-110">The model and business rules</span></span>

<span data-ttu-id="fc6ca-111">El modelo de respaldo de esta API tiene dos tipos Items de entidad: y Tags .</span><span class="sxs-lookup"><span data-stu-id="fc6ca-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="fc6ca-112">tienen un nombre que distingue entre mayúsculas y minúsculas y una colección de Tags .</span><span class="sxs-lookup"><span data-stu-id="fc6ca-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="fc6ca-113">Cada Tag una tiene una etiqueta y un recuento que representa el número de veces que se ha aplicado a Item .</span><span class="sxs-lookup"><span data-stu-id="fc6ca-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="fc6ca-114">Cada Item una de ellas solo debe tener una Tag con una etiqueta determinada.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="fc6ca-115">Si un elemento se etiqueta con la misma etiqueta más de una vez, se incrementa el recuento de la etiqueta existente con esa etiqueta en lugar de crear una nueva etiqueta.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="fc6ca-116">La eliminación de Item debe eliminar todos los asociados Tags .</span><span class="sxs-lookup"><span data-stu-id="fc6ca-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="fc6ca-117">El Item tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="fc6ca-117">The Item entity type</span></span>

<span data-ttu-id="fc6ca-118">El `Item` tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="fc6ca-119">Y su configuración en `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="fc6ca-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="fc6ca-120">Observe que el tipo de entidad restringe la manera en que se puede usar para reflejar el modelo de dominio y las reglas de negocios.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="fc6ca-121">En concreto:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-121">In particular:</span></span>
- <span data-ttu-id="fc6ca-122">La clave principal se asigna directamente al `_id` campo y no se expone públicamente.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="fc6ca-123">EF detecta y usa el constructor privado que acepta el valor y el nombre de la clave principal.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="fc6ca-124">La `Name` propiedad es de solo lectura y se establece solo en el constructor.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="fc6ca-125">se exponen como `IReadOnlyList<Tag>` para evitar modificaciones arbitrarias.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="fc6ca-126">EF asocia el `Tags` nombre de la propiedad con el `_tags` campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="fc6ca-127">El `AddTag` método toma una etiqueta de etiqueta e implementa la regla de negocios que se ha descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="fc6ca-128">Es decir, solo se agrega una etiqueta para las etiquetas nuevas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="fc6ca-129">En caso contrario, se incrementa el recuento de una etiqueta existente.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="fc6ca-130">La `Tags` propiedad de navegación se configura para una relación de varios a uno</span><span class="sxs-lookup"><span data-stu-id="fc6ca-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="fc6ca-131">No es necesario que una propiedad de navegación de Tag a Item , por lo que no se incluye.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="fc6ca-132">Además, no Tag define una propiedad de clave externa.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="fc6ca-133">En su lugar, EF creará y administrará una propiedad en el estado de sombra.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="fc6ca-134">El Tag tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="fc6ca-134">The Tag entity type</span></span>

<span data-ttu-id="fc6ca-135">El `Tag` tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="fc6ca-136">Y su configuración en `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="fc6ca-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="fc6ca-137">De forma similar a Item , Tag oculta su clave principal y hace que la `Label` propiedad sea de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="fc6ca-138">ItemsController</span><span class="sxs-lookup"><span data-stu-id="fc6ca-138">The ItemsController</span></span>

<span data-ttu-id="fc6ca-139">El controlador de API Web es bastante básico.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="fc6ca-140">Obtiene un `DbContext` del contenedor de inserción de dependencias a través de la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="fc6ca-141">Tiene métodos para obtener todos Items o un Item con un nombre determinado:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="fc6ca-142">Tiene un método para agregar un nuevo Item :</span><span class="sxs-lookup"><span data-stu-id="fc6ca-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="fc6ca-143">Un método para etiquetar un Item con una etiqueta:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="fc6ca-144">Y un método para eliminar un Item y todos los asociados Tags :</span><span class="sxs-lookup"><span data-stu-id="fc6ca-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="fc6ca-145">La mayoría de la validación y el control de errores se han quitado para reducir la confusión.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="fc6ca-146">Las pruebas</span><span class="sxs-lookup"><span data-stu-id="fc6ca-146">The Tests</span></span>

<span data-ttu-id="fc6ca-147">Las pruebas se organizan para ejecutarse con varias configuraciones de proveedor de base de datos:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="fc6ca-148">Proveedor de SQL Server, que es el proveedor que usa la aplicación.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="fc6ca-149">Proveedor de SQLite</span><span class="sxs-lookup"><span data-stu-id="fc6ca-149">The SQLite provider</span></span>
* <span data-ttu-id="fc6ca-150">Proveedor de SQLite que usa bases de datos de SQLite en memoria</span><span class="sxs-lookup"><span data-stu-id="fc6ca-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="fc6ca-151">El proveedor de base de datos de EF en memoria</span><span class="sxs-lookup"><span data-stu-id="fc6ca-151">The EF in-memory database provider</span></span>

<span data-ttu-id="fc6ca-152">Esto se logra colocando todas las pruebas en una clase base y, a continuación, heredando de esta para probar con cada proveedor.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="fc6ca-153">Tendrá que cambiar el SQL Server cadena de conexión si no está usando LocalDB.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="fc6ca-154">Consulte [pruebas con SQLite](xref:core/miscellaneous/testing/sqlite) para obtener instrucciones sobre el uso de SQLite para realizar pruebas en memoria.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="fc6ca-155">Se espera que se produzcan errores en las dos pruebas siguientes:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="fc6ca-156">`Can_remove_item_and_all_associated_tags`al ejecutar con el proveedor de base de datos de EF en memoria</span><span class="sxs-lookup"><span data-stu-id="fc6ca-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="fc6ca-157">`Can_add_item_differing_only_by_case`al ejecutar con el proveedor de SQL Server</span><span class="sxs-lookup"><span data-stu-id="fc6ca-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="fc6ca-158">Esto se describe con más detalle a continuación.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="fc6ca-159">Configurar y propagar la base de datos</span><span class="sxs-lookup"><span data-stu-id="fc6ca-159">Setting up and seeding the database</span></span>

<span data-ttu-id="fc6ca-160">XUnit, al igual que la mayoría de los marcos de pruebas, creará una nueva instancia de clase de prueba para cada serie de pruebas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="fc6ca-161">Además, XUnit no ejecutará en paralelo las pruebas de una clase de prueba determinada.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="fc6ca-162">Esto significa que se puede instalar y configurar la base de datos en el constructor de prueba y que estará en un estado conocido para cada prueba.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="fc6ca-163">En este ejemplo se vuelve a crear la base de datos para cada prueba.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="fc6ca-164">Esto funciona bien para las pruebas de base de datos en memoria de SQLite y EF, pero puede suponer una sobrecarga significativa con otros sistemas de base de datos, como SQL Server.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="fc6ca-165">Los enfoques para reducir esta sobrecarga se abordan en el [uso compartido de bases de datos entre pruebas](xref:core/miscellaneous/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="fc6ca-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="fc6ca-166">Cuando se ejecuta cada prueba:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-166">When each test is run:</span></span>
* <span data-ttu-id="fc6ca-167">DbContextOptions se configuran para el proveedor en uso y se pasan al constructor de clase base</span><span class="sxs-lookup"><span data-stu-id="fc6ca-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="fc6ca-168">Estas opciones se almacenan en una propiedad y se usan en las pruebas para la creación de instancias de DbContext.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="fc6ca-169">Se llama a un método de inicialización para crear e inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="fc6ca-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="fc6ca-170">El método de inicialización garantiza que la base de datos está limpia al eliminarla y volver a crearla.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="fc6ca-171">Algunas entidades de prueba conocidas se crean y se guardan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="fc6ca-172">Cada clase de prueba concreta hereda de este.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="fc6ca-173">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="fc6ca-174">Estructura de prueba</span><span class="sxs-lookup"><span data-stu-id="fc6ca-174">Test structure</span></span>

<span data-ttu-id="fc6ca-175">Aunque la aplicación usa la inserción de dependencias, las pruebas no lo hacen.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="fc6ca-176">Sería adecuado usar la inserción de dependencias aquí, pero el código adicional que requiere tiene poco valor.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="fc6ca-177">En su lugar, se crea un DbContext usando `new` y, a continuación, se pasa directamente como la dependencia al controlador.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="fc6ca-178">Después, cada prueba ejecuta el método sometido a prueba en el controlador y valida que los resultados son los esperados.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="fc6ca-179">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="fc6ca-180">Observe que se usan diferentes instancias de DbContext para inicializar la base de datos y ejecutar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="fc6ca-181">Esto garantiza que la prueba no esté usando (o pasando por) entidades cuyo seguimiento realiza el contexto al realizar la propagación.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="fc6ca-182">También mejor coincide con lo que ocurre en servicios y aplicaciones Web.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="fc6ca-183">Las pruebas que mutan la base de datos crean una segunda instancia de DbContext en la prueba por motivos similares.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="fc6ca-184">Es decir, crear un nuevo contexto, limpiar y, a continuación, leerlo desde la base de datos para asegurarse de que los cambios se guardaron en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="fc6ca-185">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="fc6ca-186">Dos pruebas ligeramente más complicadas cubren la lógica de negocios en torno a la adición de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="fc6ca-187">Problemas con diferentes proveedores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="fc6ca-187">Issues using different database providers</span></span>

<span data-ttu-id="fc6ca-188">La prueba con un sistema de base de datos diferente al que se usa en la aplicación de producción puede provocar problemas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="fc6ca-189">Estos se describen en el nivel conceptual del [código de prueba que usa EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="fc6ca-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="fc6ca-190">En las secciones siguientes se incluyen dos ejemplos de estos problemas que se muestran en las pruebas de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="fc6ca-191">La prueba se supera cuando se interrumpe la aplicación</span><span class="sxs-lookup"><span data-stu-id="fc6ca-191">Test passes when the application is broken</span></span>

<span data-ttu-id="fc6ca-192">Uno de los requisitos de nuestra aplicación es que " Items tiene un nombre que distingue entre mayúsculas y minúsculas y una colección de Tags ".</span><span class="sxs-lookup"><span data-stu-id="fc6ca-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="fc6ca-193">Esto es bastante sencillo de probar:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="fc6ca-194">La ejecución de esta prueba en la base de datos en memoria de EF indica que todo está bien.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="fc6ca-195">Todo sigue teniendo el aspecto correcto al usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="fc6ca-196">Pero se produce un error en la prueba cuando se ejecuta en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="fc6ca-197">Esto se debe a que la base de datos de EF en memoria y la base de datos de SQLite distinguen mayúsculas de minúsculas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="fc6ca-198">SQL Server, por otro lado, no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="fc6ca-199">EF Core, por diseño, no cambia estos comportamientos porque forzar un cambio en la distinción de mayúsculas y minúsculas puede tener un gran impacto en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="fc6ca-200">Una vez que sabemos que se trata de un problema, podemos corregir la aplicación y compensar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="fc6ca-201">Sin embargo, el punto aquí es que este error podría perderse si solo se prueba con la base de datos de EF en memoria o con proveedores de SQLite.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="fc6ca-202">Se produce un error en la prueba cuando la aplicación es correcta</span><span class="sxs-lookup"><span data-stu-id="fc6ca-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="fc6ca-203">Otro de los requisitos para nuestra aplicación es que "la eliminación de un Item debe eliminar todos los asociados Tags ".</span><span class="sxs-lookup"><span data-stu-id="fc6ca-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="fc6ca-204">De nuevo, fácil de probar:</span><span class="sxs-lookup"><span data-stu-id="fc6ca-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="fc6ca-205">Esta prueba se supera en SQL Server y SQLite, pero produce un error con la base de datos de EF en memoria.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="fc6ca-206">En este caso, la aplicación funciona correctamente porque SQL Server admite [eliminaciones en cascada](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="fc6ca-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="fc6ca-207">SQLite también admite eliminaciones en cascada, al igual que la mayoría de las bases de datos relacionales, por lo que la prueba en SQLite funciona.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="fc6ca-208">Por otro lado, la base de datos en memoria de EF [no admite eliminaciones en cascada](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="fc6ca-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="fc6ca-209">Esto significa que esta parte de la aplicación no se puede probar con el proveedor de base de datos de EF en memoria.</span><span class="sxs-lookup"><span data-stu-id="fc6ca-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
