---
title: "Actualización de EF principales 1.0 RC2 a RTM - Core EF"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
ms.technology: entity-framework-core
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 7a1d85949a5f9e1ad7efdbf585a608d815e8ce63
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="9e582-102">Actualización de EF Core 1.0 RC2 a RTM</span><span class="sxs-lookup"><span data-stu-id="9e582-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="9e582-103">Este artículo proporcionan instrucciones para mover una aplicación compilada con los paquetes de RC2 en 1.0.0 RTM.</span><span class="sxs-lookup"><span data-stu-id="9e582-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="9e582-104">Versiones del paquete</span><span class="sxs-lookup"><span data-stu-id="9e582-104">Package Versions</span></span>

<span data-ttu-id="9e582-105">Los nombres de los paquetes de nivel superior que instala normalmente en una aplicación no cambió de RC2 y RTM.</span><span class="sxs-lookup"><span data-stu-id="9e582-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="9e582-106">**Debe actualizar los paquetes instalados a las versiones RTM:**</span><span class="sxs-lookup"><span data-stu-id="9e582-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="9e582-107">Paquetes en tiempo de ejecución (por ejemplo, `Microsoft.EntityFrameworkCore.SqlServer`) cambió de `1.0.0-rc2-final` a `1.0.0`.</span><span class="sxs-lookup"><span data-stu-id="9e582-107">Runtime packages (e.g. `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="9e582-108">El `Microsoft.EntityFrameworkCore.Tools` paquete cambió de `1.0.0-preview1-final` a `1.0.0-preview2-final`.</span><span class="sxs-lookup"><span data-stu-id="9e582-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="9e582-109">Tenga en cuenta que las herramientas es aún una versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="9e582-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="9e582-110">Migraciones existentes que tenga maxLength agregado</span><span class="sxs-lookup"><span data-stu-id="9e582-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="9e582-111">En RC2, la definición de columna en una migración de aspecto `table.Column<string>(nullable: true)` y se busca la longitud de la columna en algunos metadatos se almacena en el código subyacente de la migración.</span><span class="sxs-lookup"><span data-stu-id="9e582-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="9e582-112">En RTM, la longitud se incluye ahora en el código con scaffolding `table.Column<string>(maxLength: 450, nullable: true)`.</span><span class="sxs-lookup"><span data-stu-id="9e582-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="9e582-113">Las migraciones existentes que se scaffolding antes de usar RTM no tendrán el `maxLength` argumento especificado.</span><span class="sxs-lookup"><span data-stu-id="9e582-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="9e582-114">Esto significa que se usará la longitud máxima admitida por la base de datos (`nvarchar(max)` en SQL Server).</span><span class="sxs-lookup"><span data-stu-id="9e582-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="9e582-115">Esto puede ser un problema para algunas columnas, pero las columnas que forman parte de una clave, clave externa, o índice deben actualizarse para incluir una longitud máxima.</span><span class="sxs-lookup"><span data-stu-id="9e582-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="9e582-116">Por convención, 450 es la longitud máxima usado para las claves, claves externas y las columnas indizadas.</span><span class="sxs-lookup"><span data-stu-id="9e582-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="9e582-117">Si ha configurado explícitamente una longitud en el modelo, use esa longitud en su lugar.</span><span class="sxs-lookup"><span data-stu-id="9e582-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

<span data-ttu-id="9e582-118">**Identidad de ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="9e582-118">**ASP.NET Identity**</span></span>

<span data-ttu-id="9e582-119">Este cambio afecta a proyectos que usan ASP.NET Identity y se crearon a partir un pre-RTM plantilla de proyecto.</span><span class="sxs-lookup"><span data-stu-id="9e582-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="9e582-120">La plantilla de proyecto incluye una migración que se utiliza para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9e582-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="9e582-121">Esta migración debe modificarse para especificar una longitud máxima de `256` para las columnas siguientes.</span><span class="sxs-lookup"><span data-stu-id="9e582-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

*  <span data-ttu-id="9e582-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="9e582-122">**AspNetRoles**</span></span>

    * <span data-ttu-id="9e582-123">Nombre</span><span class="sxs-lookup"><span data-stu-id="9e582-123">Name</span></span>

    * <span data-ttu-id="9e582-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="9e582-124">NormalizedName</span></span>

*  <span data-ttu-id="9e582-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="9e582-125">**AspNetUsers**</span></span>

   * <span data-ttu-id="9e582-126">Correo electrónico</span><span class="sxs-lookup"><span data-stu-id="9e582-126">Email</span></span>

   * <span data-ttu-id="9e582-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="9e582-127">NormalizedEmail</span></span>

   * <span data-ttu-id="9e582-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="9e582-128">NormalizedUserName</span></span>

   * <span data-ttu-id="9e582-129">UserName</span><span class="sxs-lookup"><span data-stu-id="9e582-129">UserName</span></span>

<span data-ttu-id="9e582-130">Si no se realiza este cambio se producirá la excepción siguiente cuando la migración inicial se aplica a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="9e582-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="9e582-131">.NET core: Quitar "importaciones" en project.json</span><span class="sxs-lookup"><span data-stu-id="9e582-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="9e582-132">Si se establece como destino .NET Core con RC2, había que agregar `imports` a project.json como solución temporal para algunas de las dependencias del núcleo de EF no son compatibles con .NET estándar.</span><span class="sxs-lookup"><span data-stu-id="9e582-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="9e582-133">Ahora puede quitar.</span><span class="sxs-lookup"><span data-stu-id="9e582-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="9e582-134">UWP: Agregar redirecciones de enlace</span><span class="sxs-lookup"><span data-stu-id="9e582-134">UWP: Add binding redirects</span></span>

<span data-ttu-id="9e582-135">Si tratas de ejecutar comandos EF en resultados de proyectos de plataforma Universal de Windows (UWP) en el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="9e582-135">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

<span data-ttu-id="9e582-136">Debe agregar manualmente las redirecciones de enlace para el proyecto de UWP.</span><span class="sxs-lookup"><span data-stu-id="9e582-136">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="9e582-137">Cree un archivo denominado `App.config` en el proyecto de la carpeta raíz y agrega redirecciones a las versiones de ensamblado correcto.</span><span class="sxs-lookup"><span data-stu-id="9e582-137">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

``` xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```