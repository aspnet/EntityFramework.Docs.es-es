---
title: 'Propagación de datos: EF Core'
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 5c056c600f696ad1443ddb7b8c95c4b0ead06d21
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414576"
---
# <a name="data-seeding"></a><span data-ttu-id="968ee-102">Propagación de datos</span><span class="sxs-lookup"><span data-stu-id="968ee-102">Data Seeding</span></span>

<span data-ttu-id="968ee-103">La propagación de datos es el proceso de rellenar una base de datos con un conjunto inicial de datos.</span><span class="sxs-lookup"><span data-stu-id="968ee-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="968ee-104">Hay varias maneras de lograrlo en EF Core:</span><span class="sxs-lookup"><span data-stu-id="968ee-104">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="968ee-105">Datos de inicialización del modelo</span><span class="sxs-lookup"><span data-stu-id="968ee-105">Model seed data</span></span>
* <span data-ttu-id="968ee-106">Personalización de la migración manual</span><span class="sxs-lookup"><span data-stu-id="968ee-106">Manual migration customization</span></span>
* <span data-ttu-id="968ee-107">Lógica de inicialización personalizada</span><span class="sxs-lookup"><span data-stu-id="968ee-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="968ee-108">Datos de inicialización del modelo</span><span class="sxs-lookup"><span data-stu-id="968ee-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="968ee-109">Esta característica es nueva en EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="968ee-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="968ee-110">A diferencia de EF6, en EF Core, la propagación de datos se puede asociar a un tipo de entidad como parte de la configuración del modelo.</span><span class="sxs-lookup"><span data-stu-id="968ee-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="968ee-111">A continuación, las [migraciones](xref:core/managing-schemas/migrations/index) de EF Core pueden calcular automáticamente las operaciones de inserción, actualización o eliminación que se deben aplicar al actualizar la base de datos a una nueva versión del modelo.</span><span class="sxs-lookup"><span data-stu-id="968ee-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="968ee-112">Las migraciones solo tienen en cuenta los cambios del modelo al determinar qué operación se debe realizar para obtener los datos de inicialización en el estado deseado.</span><span class="sxs-lookup"><span data-stu-id="968ee-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="968ee-113">Por lo tanto, es posible que se pierdan los cambios realizados en los datos fuera de las migraciones o se produzca un error.</span><span class="sxs-lookup"><span data-stu-id="968ee-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="968ee-114">Por ejemplo, se configurarán los datos de inicialización de una `Blog` en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="968ee-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="968ee-115">Para agregar entidades que tienen una relación, es necesario especificar los valores de clave externa:</span><span class="sxs-lookup"><span data-stu-id="968ee-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="968ee-116">Si el tipo de entidad tiene propiedades en el estado de sombra, se puede usar una clase anónima para proporcionar los valores:</span><span class="sxs-lookup"><span data-stu-id="968ee-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="968ee-117">Los tipos de entidad de propiedad se pueden inicializar de una manera similar:</span><span class="sxs-lookup"><span data-stu-id="968ee-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="968ee-118">Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) para obtener más contexto.</span><span class="sxs-lookup"><span data-stu-id="968ee-118">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="968ee-119">Una vez que se han agregado los datos al modelo, se deben usar las [migraciones](xref:core/managing-schemas/migrations/index) para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="968ee-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="968ee-120">Si necesita aplicar las migraciones como parte de una implementación automatizada, puede [crear un script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) que se pueda obtener como vista previa antes de la ejecución.</span><span class="sxs-lookup"><span data-stu-id="968ee-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="968ee-121">Como alternativa, puede usar `context.Database.EnsureCreated()` para crear una nueva base de datos que contenga los datos de inicialización, por ejemplo, para una base de datos de prueba o cuando se usa el proveedor en memoria o cualquier base de datos que no sea de relación.</span><span class="sxs-lookup"><span data-stu-id="968ee-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="968ee-122">Tenga en cuenta que si la base de datos ya existe, `EnsureCreated()` no actualizará el esquema ni los datos de inicialización en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="968ee-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="968ee-123">En el caso de las bases de datos relacionales, no debe llamar a `EnsureCreated()` si tiene previsto usar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="968ee-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="968ee-124">Limitaciones de los datos de inicialización del modelo</span><span class="sxs-lookup"><span data-stu-id="968ee-124">Limitations of model seed data</span></span>

<span data-ttu-id="968ee-125">Este tipo de datos de inicialización se administra mediante migraciones y el script para actualizar los datos que ya están en la base de datos debe generarse sin necesidad de conectarse a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="968ee-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="968ee-126">Esto impone algunas restricciones:</span><span class="sxs-lookup"><span data-stu-id="968ee-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="968ee-127">El valor de clave principal debe especificarse incluso si la base de datos lo genera normalmente.</span><span class="sxs-lookup"><span data-stu-id="968ee-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="968ee-128">Se usará para detectar los cambios de datos entre las migraciones.</span><span class="sxs-lookup"><span data-stu-id="968ee-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="968ee-129">Los datos previamente inicializados se quitarán si se cambia la clave principal de cualquier manera.</span><span class="sxs-lookup"><span data-stu-id="968ee-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="968ee-130">Por lo tanto, esta característica es muy útil para los datos estáticos que no se espera que cambien fuera de las migraciones y no depende de nada más en la base de datos, por ejemplo códigos postales.</span><span class="sxs-lookup"><span data-stu-id="968ee-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="968ee-131">Si el escenario incluye alguno de los siguientes, se recomienda usar la lógica de inicialización personalizada que se describe en la última sección:</span><span class="sxs-lookup"><span data-stu-id="968ee-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="968ee-132">Datos temporales para pruebas</span><span class="sxs-lookup"><span data-stu-id="968ee-132">Temporary data for testing</span></span>
* <span data-ttu-id="968ee-133">Datos que dependen del estado de la base de datos</span><span class="sxs-lookup"><span data-stu-id="968ee-133">Data that depends on database state</span></span>
* <span data-ttu-id="968ee-134">Datos que necesitan que la base de datos genere valores clave, incluidas las entidades que usan claves alternativas como identidad.</span><span class="sxs-lookup"><span data-stu-id="968ee-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="968ee-135">Datos que requieren una transformación personalizada (que no se controlan mediante [conversiones de valores](xref:core/modeling/value-conversions)), como algunas operaciones hash de contraseñas.</span><span class="sxs-lookup"><span data-stu-id="968ee-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="968ee-136">Datos que requieren llamadas a la API externa, como ASP.NET Core roles de identidad y la creación de usuarios</span><span class="sxs-lookup"><span data-stu-id="968ee-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="968ee-137">Personalización de la migración manual</span><span class="sxs-lookup"><span data-stu-id="968ee-137">Manual migration customization</span></span>

<span data-ttu-id="968ee-138">Cuando se agrega una migración, los cambios en los datos especificados con `HasData` se transforman en llamadas a `InsertData()`, `UpdateData()`y `DeleteData()`.</span><span class="sxs-lookup"><span data-stu-id="968ee-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="968ee-139">Una manera de resolver algunas de las limitaciones de `HasData` es agregar manualmente estas llamadas o [operaciones personalizadas](xref:core/managing-schemas/migrations/operations) a la migración.</span><span class="sxs-lookup"><span data-stu-id="968ee-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="968ee-140">Lógica de inicialización personalizada</span><span class="sxs-lookup"><span data-stu-id="968ee-140">Custom initialization logic</span></span>

<span data-ttu-id="968ee-141">Una manera sencilla y eficaz de realizar la propagación de datos es usar [`DbContext.SaveChanges()`](xref:core/saving/index) antes de que la lógica de la aplicación principal comience la ejecución.</span><span class="sxs-lookup"><span data-stu-id="968ee-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="968ee-142">El código de propagación no debe formar parte de la ejecución normal de la aplicación, ya que esto puede provocar problemas de simultaneidad cuando se ejecutan varias instancias y también requeriría que la aplicación tuviera permiso para modificar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="968ee-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="968ee-143">En función de las restricciones de la implementación, el código de inicialización se puede ejecutar de diferentes maneras:</span><span class="sxs-lookup"><span data-stu-id="968ee-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="968ee-144">Ejecución local de la aplicación de inicialización</span><span class="sxs-lookup"><span data-stu-id="968ee-144">Running the initialization app locally</span></span>
* <span data-ttu-id="968ee-145">Implementar la aplicación de inicialización con la aplicación principal, invocar la rutina de inicialización y deshabilitar o quitar la aplicación de inicialización.</span><span class="sxs-lookup"><span data-stu-id="968ee-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="968ee-146">Normalmente, esto se puede automatizar mediante el uso de [perfiles de publicación](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="968ee-146">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
