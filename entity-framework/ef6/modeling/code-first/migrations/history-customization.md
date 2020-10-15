---
title: 'Personalización de la tabla de historial de migraciones: EF6'
description: Personalización de la tabla de historial de migraciones en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: f0f474507659d5fbe43ecf2ed7216fb5d1a86779
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066400"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="a554b-103">Personalización de la tabla de historial de migraciones</span><span class="sxs-lookup"><span data-stu-id="a554b-103">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="a554b-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="a554b-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a554b-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="a554b-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="a554b-106">En este artículo se supone que sabe cómo usar Migraciones de Code First en escenarios básicos.</span><span class="sxs-lookup"><span data-stu-id="a554b-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="a554b-107">Si no lo hace, tendrá que leer [migraciones de Code First](xref:ef6/modeling/code-first/migrations/index) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="a554b-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="a554b-108">¿Qué es la tabla de historial de migraciones?</span><span class="sxs-lookup"><span data-stu-id="a554b-108">What is Migrations History Table?</span></span>

<span data-ttu-id="a554b-109">La tabla de historial de migraciones es una tabla que usa Migraciones de Code First para almacenar detalles sobre las migraciones que se aplican a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a554b-109">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="a554b-110">De forma predeterminada, el nombre de la tabla de la base de datos es \_ \_ MigrationHistory y se crea al aplicar la primera migración a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a554b-110">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="a554b-111">En Entity Framework 5, esta tabla era una tabla del sistema si la aplicación usaba la base de datos de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a554b-111">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="a554b-112">No obstante, esto ha cambiado en Entity Framework 6 y la tabla de historial de migraciones ya no está marcada como una tabla del sistema.</span><span class="sxs-lookup"><span data-stu-id="a554b-112">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="a554b-113">¿Por qué personalizar la tabla de historial de migraciones?</span><span class="sxs-lookup"><span data-stu-id="a554b-113">Why customize Migrations History Table?</span></span>

<span data-ttu-id="a554b-114">La tabla de historial de migraciones se supone que se usará únicamente en Migraciones de Code First y cambiarla manualmente puede interrumpir las migraciones.</span><span class="sxs-lookup"><span data-stu-id="a554b-114">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="a554b-115">Sin embargo, a veces la configuración predeterminada no es adecuada y la tabla debe personalizarse, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a554b-115">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="a554b-116">Debe cambiar los nombres y/o las caras de las columnas para habilitar un proveedor de migraciones<sup>de terceras partes.</sup></span><span class="sxs-lookup"><span data-stu-id="a554b-116">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="a554b-117">Desea cambiar el nombre de la tabla</span><span class="sxs-lookup"><span data-stu-id="a554b-117">You want to change the name of the table</span></span>
-   <span data-ttu-id="a554b-118">Debe usar un esquema no predeterminado para la \_ \_ tabla MigrationHistory</span><span class="sxs-lookup"><span data-stu-id="a554b-118">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="a554b-119">Necesita almacenar datos adicionales para una versión determinada del contexto y, por tanto, necesita agregar una columna adicional a la tabla.</span><span class="sxs-lookup"><span data-stu-id="a554b-119">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="a554b-120">Palabras de precaución</span><span class="sxs-lookup"><span data-stu-id="a554b-120">Words of precaution</span></span>

<span data-ttu-id="a554b-121">Cambiar la tabla de historial de migración es eficaz, pero debe tener cuidado de no abusar.</span><span class="sxs-lookup"><span data-stu-id="a554b-121">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="a554b-122">En la actualidad, el tiempo de ejecución de EF no comprueba si la tabla de historial de migraciones personalizada es compatible con el tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a554b-122">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="a554b-123">Si no es así, es posible que la aplicación se interrumpa en tiempo de ejecución o se comporte de maneras imprevisibles.</span><span class="sxs-lookup"><span data-stu-id="a554b-123">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="a554b-124">Esto es aún más importante si usa varios contextos por base de datos, en cuyo caso varios contextos pueden usar la misma tabla de historial de migración para almacenar información acerca de las migraciones.</span><span class="sxs-lookup"><span data-stu-id="a554b-124">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="a554b-125">¿Cómo se personaliza la tabla de historial de migraciones?</span><span class="sxs-lookup"><span data-stu-id="a554b-125">How to customize Migrations History Table?</span></span>

<span data-ttu-id="a554b-126">Antes de empezar, debe saber que solo puede personalizar la tabla de historial de migraciones antes de aplicar la primera migración.</span><span class="sxs-lookup"><span data-stu-id="a554b-126">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="a554b-127">Ahora, en el código.</span><span class="sxs-lookup"><span data-stu-id="a554b-127">Now, to the code.</span></span>

<span data-ttu-id="a554b-128">En primer lugar, tendrá que crear una clase derivada de la clase System. Data. Entity. Migrations. History. HistoryContext.</span><span class="sxs-lookup"><span data-stu-id="a554b-128">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="a554b-129">La clase HistoryContext se deriva de la clase DbContext, por lo que la configuración de la tabla de historial de migraciones es muy similar a la configuración de modelos EF con la API fluida.</span><span class="sxs-lookup"><span data-stu-id="a554b-129">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="a554b-130">Solo tiene que invalidar el método OnModelCreating y usar la API fluida para configurar la tabla.</span><span class="sxs-lookup"><span data-stu-id="a554b-130">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="a554b-131">Normalmente, cuando se configuran modelos EF no es necesario llamar a base. OnModelCreating () del método OnModelCreating invalidado, ya que DbContext. OnModelCreating () tiene un cuerpo vacío.</span><span class="sxs-lookup"><span data-stu-id="a554b-131">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="a554b-132">Este no es el caso cuando se configura la tabla de historial de migraciones.</span><span class="sxs-lookup"><span data-stu-id="a554b-132">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="a554b-133">En este caso, lo primero que hay que hacer en la invalidación de OnModelCreating () es, en realidad, llamar a base. OnModelCreating ().</span><span class="sxs-lookup"><span data-stu-id="a554b-133">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="a554b-134">De esta forma, se configurará la tabla de historial de migraciones de la manera predeterminada que se va a ajustar en el método de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="a554b-134">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="a554b-135">Supongamos que desea cambiar el nombre de la tabla de historial de migraciones y colocarla en un esquema personalizado denominado "admin".</span><span class="sxs-lookup"><span data-stu-id="a554b-135">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="a554b-136">Además, el DBA le gustaría cambiar el nombre de la columna MigrationId a \_ ID. de migración.</span><span class="sxs-lookup"><span data-stu-id="a554b-136">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="a554b-137">Para ello, cree la clase siguiente derivada de HistoryContext:</span><span class="sxs-lookup"><span data-stu-id="a554b-137"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

<span data-ttu-id="a554b-138">Una vez que el HistoryContext personalizado esté listo, debe hacer que EF lo tenga al registrarlo mediante [la configuración basada en código](https://msdn.com/data/jj680699):</span><span class="sxs-lookup"><span data-stu-id="a554b-138">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

<span data-ttu-id="a554b-139">Eso es prácticamente todo.</span><span class="sxs-lookup"><span data-stu-id="a554b-139">That’s pretty much it.</span></span> <span data-ttu-id="a554b-140">Ahora puede ir a la consola del administrador de paquetes, enable-Migrations, Add-Migration y, por último, Update-Database.</span><span class="sxs-lookup"><span data-stu-id="a554b-140">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="a554b-141">Esto debería hacer que se agregue a la base de datos una tabla de historial de migraciones configurada según los detalles especificados en la clase derivada HistoryContext.</span><span class="sxs-lookup"><span data-stu-id="a554b-141">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![Tabla de historial de migraciones](~/ef6/media/database.png)
