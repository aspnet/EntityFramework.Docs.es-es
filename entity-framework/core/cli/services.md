---
title: Servicios en tiempo de diseño-EF Core
description: Información sobre los servicios en tiempo de diseño de Entity Framework Core
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431354"
---
# <a name="design-time-services"></a><span data-ttu-id="d451d-103">Servicios en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="d451d-103">Design-time services</span></span>

<span data-ttu-id="d451d-104">Algunos servicios usados por las herramientas solo se usan en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="d451d-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="d451d-105">Estos servicios se administran de forma independiente de los servicios en tiempo de ejecución de EF Core para evitar que se implementen con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d451d-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="d451d-106">Para invalidar uno de estos servicios (por ejemplo, el servicio para generar archivos de migración), agregue una implementación de `IDesignTimeServices` al proyecto de inicio.</span><span class="sxs-lookup"><span data-stu-id="d451d-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="d451d-107">Referencia a Microsoft. EntityFrameworkCore. Design</span><span class="sxs-lookup"><span data-stu-id="d451d-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="d451d-108">Microsoft. EntityFrameworkCore. Design es un paquete de DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="d451d-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="d451d-109">Esto significa que la dependencia no fluye de manera transitiva en otros proyectos y que, de forma predeterminada, no puede hacer referencia a sus tipos.</span><span class="sxs-lookup"><span data-stu-id="d451d-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="d451d-110">Para hacer referencia a sus tipos e invalidar los servicios en tiempo de diseño, actualice los metadatos del elemento PackageReference en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="d451d-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="d451d-111">Si se hace referencia al paquete de forma transitiva a través de Microsoft. EntityFrameworkCore. Tools, tendrá que agregar un PackageReference explícito al paquete y cambiar sus metadatos.</span><span class="sxs-lookup"><span data-stu-id="d451d-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="d451d-112">Lista de servicios</span><span class="sxs-lookup"><span data-stu-id="d451d-112">List of services</span></span>

<span data-ttu-id="d451d-113">A continuación se muestra una lista de los servicios en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="d451d-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="d451d-114">Servicio</span><span class="sxs-lookup"><span data-stu-id="d451d-114">Service</span></span>                                                                              | <span data-ttu-id="d451d-115">Descripción</span><span class="sxs-lookup"><span data-stu-id="d451d-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="d451d-116">Genera el código para las anotaciones del modelo correspondientes.</span><span class="sxs-lookup"><span data-stu-id="d451d-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="d451d-117">Ayuda a generar código de C#.</span><span class="sxs-lookup"><span data-stu-id="d451d-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="d451d-118">Palabras singular y plural nombres.</span><span class="sxs-lookup"><span data-stu-id="d451d-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="d451d-119">Genera código para una migración.</span><span class="sxs-lookup"><span data-stu-id="d451d-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="d451d-120">La clase principal para administrar los archivos de migración.</span><span class="sxs-lookup"><span data-stu-id="d451d-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="d451d-121">Crea un modelo de base de datos a partir de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="d451d-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="d451d-122">Genera código para un modelo.</span><span class="sxs-lookup"><span data-stu-id="d451d-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="d451d-123">Genera código de configuración.</span><span class="sxs-lookup"><span data-stu-id="d451d-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="d451d-124">La clase principal para la scaffolding de modelos con ingeniería inversa.</span><span class="sxs-lookup"><span data-stu-id="d451d-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="d451d-125">Usar servicios</span><span class="sxs-lookup"><span data-stu-id="d451d-125">Using services</span></span>

<span data-ttu-id="d451d-126">Estos servicios también pueden ser útiles para crear sus propias herramientas.</span><span class="sxs-lookup"><span data-stu-id="d451d-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="d451d-127">Por ejemplo, si desea automatizar parte del flujo de trabajo de su tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="d451d-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="d451d-128">Puede crear un proveedor de servicios que contenga estos servicios mediante los métodos de extensión AddEntityFrameworkDesignTimeServices y AddDbContextDesignTimeServices.</span><span class="sxs-lookup"><span data-stu-id="d451d-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
