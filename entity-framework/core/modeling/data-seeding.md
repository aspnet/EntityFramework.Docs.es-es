---
title: 'Propagación de datos: EF Core'
description: Usar la inicialización de datos para rellenar una base de datos con un conjunto inicial de datos mediante Entity Framework Core
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: fefa6232496cd250d52a436971251f59af09f5c6
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429681"
---
# <a name="data-seeding"></a><span data-ttu-id="d931d-103">Propagación de datos</span><span class="sxs-lookup"><span data-stu-id="d931d-103">Data Seeding</span></span>

<span data-ttu-id="d931d-104">La propagación de datos es el proceso de rellenar una base de datos con un conjunto inicial de datos.</span><span class="sxs-lookup"><span data-stu-id="d931d-104">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="d931d-105">Hay varias maneras de lograrlo en EF Core:</span><span class="sxs-lookup"><span data-stu-id="d931d-105">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="d931d-106">Datos de inicialización del modelo</span><span class="sxs-lookup"><span data-stu-id="d931d-106">Model seed data</span></span>
* <span data-ttu-id="d931d-107">Personalización de la migración manual</span><span class="sxs-lookup"><span data-stu-id="d931d-107">Manual migration customization</span></span>
* <span data-ttu-id="d931d-108">Lógica de inicialización personalizada</span><span class="sxs-lookup"><span data-stu-id="d931d-108">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="d931d-109">Datos de inicialización del modelo</span><span class="sxs-lookup"><span data-stu-id="d931d-109">Model seed data</span></span>

<span data-ttu-id="d931d-110">A diferencia de EF6, en EF Core, la propagación de datos se puede asociar a un tipo de entidad como parte de la configuración del modelo.</span><span class="sxs-lookup"><span data-stu-id="d931d-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="d931d-111">A continuación, las [migraciones](xref:core/managing-schemas/migrations/index) de EF Core pueden calcular automáticamente las operaciones de inserción, actualización o eliminación que se deben aplicar al actualizar la base de datos a una nueva versión del modelo.</span><span class="sxs-lookup"><span data-stu-id="d931d-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="d931d-112">Las migraciones solo tienen en cuenta los cambios del modelo al determinar qué operación se debe realizar para obtener los datos de inicialización en el estado deseado.</span><span class="sxs-lookup"><span data-stu-id="d931d-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="d931d-113">Por lo tanto, es posible que se pierdan los cambios realizados en los datos fuera de las migraciones o se produzca un error.</span><span class="sxs-lookup"><span data-stu-id="d931d-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="d931d-114">Como ejemplo, se configurarán los datos de inicialización de un `Blog` en `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="d931d-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="d931d-115">Para agregar entidades que tienen una relación, es necesario especificar los valores de clave externa:</span><span class="sxs-lookup"><span data-stu-id="d931d-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="d931d-116">Si el tipo de entidad tiene propiedades en el estado de sombra, se puede usar una clase anónima para proporcionar los valores:</span><span class="sxs-lookup"><span data-stu-id="d931d-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="d931d-117">Los tipos de entidad de propiedad se pueden inicializar de una manera similar:</span><span class="sxs-lookup"><span data-stu-id="d931d-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="d931d-118">Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) para obtener más contexto.</span><span class="sxs-lookup"><span data-stu-id="d931d-118">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="d931d-119">Una vez que se han agregado los datos al modelo, se deben usar las [migraciones](xref:core/managing-schemas/migrations/index) para aplicar los cambios.</span><span class="sxs-lookup"><span data-stu-id="d931d-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="d931d-120">Si necesita aplicar las migraciones como parte de una implementación automatizada, puede [crear un script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) que se pueda obtener como vista previa antes de la ejecución.</span><span class="sxs-lookup"><span data-stu-id="d931d-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="d931d-121">Como alternativa, puede usar `context.Database.EnsureCreated()` para crear una nueva base de datos que contenga los datos de inicialización, por ejemplo, para una base de datos de prueba o cuando se usa el proveedor en memoria o cualquier base de datos que no sea de relación.</span><span class="sxs-lookup"><span data-stu-id="d931d-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="d931d-122">Tenga en cuenta que si la base de datos ya existe, no `EnsureCreated()` actualizará el esquema ni los datos de inicialización en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d931d-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="d931d-123">En el caso de las bases de datos relacionales, no debe llamar a `EnsureCreated()` si planea usar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="d931d-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="d931d-124">Limitaciones de los datos de inicialización del modelo</span><span class="sxs-lookup"><span data-stu-id="d931d-124">Limitations of model seed data</span></span>

<span data-ttu-id="d931d-125">Este tipo de datos de inicialización se administra mediante migraciones y el script para actualizar los datos que ya están en la base de datos debe generarse sin necesidad de conectarse a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d931d-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="d931d-126">Esto impone algunas restricciones:</span><span class="sxs-lookup"><span data-stu-id="d931d-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="d931d-127">El valor de clave principal debe especificarse incluso si la base de datos lo genera normalmente.</span><span class="sxs-lookup"><span data-stu-id="d931d-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="d931d-128">Se usará para detectar los cambios de datos entre las migraciones.</span><span class="sxs-lookup"><span data-stu-id="d931d-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="d931d-129">Los datos previamente inicializados se quitarán si se cambia la clave principal de cualquier manera.</span><span class="sxs-lookup"><span data-stu-id="d931d-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="d931d-130">Por lo tanto, esta característica es muy útil para los datos estáticos que no se espera que cambien fuera de las migraciones y no depende de nada más en la base de datos, por ejemplo códigos postales.</span><span class="sxs-lookup"><span data-stu-id="d931d-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="d931d-131">Si el escenario incluye alguno de los siguientes, se recomienda usar la lógica de inicialización personalizada que se describe en la última sección:</span><span class="sxs-lookup"><span data-stu-id="d931d-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="d931d-132">Datos temporales para pruebas</span><span class="sxs-lookup"><span data-stu-id="d931d-132">Temporary data for testing</span></span>
* <span data-ttu-id="d931d-133">Datos que dependen del estado de la base de datos</span><span class="sxs-lookup"><span data-stu-id="d931d-133">Data that depends on database state</span></span>
* <span data-ttu-id="d931d-134">Los datos que son grandes (la propagación de datos se capturan en instantáneas de migración, y los datos grandes pueden conducir rápidamente a grandes archivos y a un rendimiento degradado).</span><span class="sxs-lookup"><span data-stu-id="d931d-134">Data that is large (seeding data gets captured in migration snapshots, and large data can quickly lead to huge files and degraded performance).</span></span>
* <span data-ttu-id="d931d-135">Datos que necesitan que la base de datos genere valores clave, incluidas las entidades que usan claves alternativas como identidad.</span><span class="sxs-lookup"><span data-stu-id="d931d-135">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="d931d-136">Datos que requieren una transformación personalizada (que no se controlan mediante [conversiones de valores](xref:core/modeling/value-conversions)), como algunas operaciones hash de contraseñas.</span><span class="sxs-lookup"><span data-stu-id="d931d-136">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="d931d-137">Datos que requieren llamadas a la API externa, como ASP.NET Core roles de identidad y la creación de usuarios</span><span class="sxs-lookup"><span data-stu-id="d931d-137">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="d931d-138">Personalización de la migración manual</span><span class="sxs-lookup"><span data-stu-id="d931d-138">Manual migration customization</span></span>

<span data-ttu-id="d931d-139">Cuando se agrega una migración, los cambios en los datos especificados con `HasData` se transforman en llamadas a `InsertData()` , `UpdateData()` y `DeleteData()` .</span><span class="sxs-lookup"><span data-stu-id="d931d-139">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="d931d-140">Una manera de resolver algunas de las limitaciones de `HasData` es agregar manualmente estas llamadas o [operaciones personalizadas](xref:core/managing-schemas/migrations/operations) a la migración.</span><span class="sxs-lookup"><span data-stu-id="d931d-140">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="d931d-141">Lógica de inicialización personalizada</span><span class="sxs-lookup"><span data-stu-id="d931d-141">Custom initialization logic</span></span>

<span data-ttu-id="d931d-142">Una manera sencilla y eficaz de realizar la propagación de datos es usar [`DbContext.SaveChanges()`](xref:core/saving/index) antes de que la lógica de la aplicación principal comience la ejecución.</span><span class="sxs-lookup"><span data-stu-id="d931d-142">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="d931d-143">El código de propagación no debe formar parte de la ejecución normal de la aplicación, ya que esto puede provocar problemas de simultaneidad cuando se ejecutan varias instancias y también requeriría que la aplicación tuviera permiso para modificar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="d931d-143">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="d931d-144">En función de las restricciones de la implementación, el código de inicialización se puede ejecutar de diferentes maneras:</span><span class="sxs-lookup"><span data-stu-id="d931d-144">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="d931d-145">Ejecución local de la aplicación de inicialización</span><span class="sxs-lookup"><span data-stu-id="d931d-145">Running the initialization app locally</span></span>
* <span data-ttu-id="d931d-146">Implementar la aplicación de inicialización con la aplicación principal, invocar la rutina de inicialización y deshabilitar o quitar la aplicación de inicialización.</span><span class="sxs-lookup"><span data-stu-id="d931d-146">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="d931d-147">Normalmente, esto se puede automatizar mediante el uso de [perfiles de publicación](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="d931d-147">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
