---
title: Actualización de EF Core 1,0 RC2 a RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526776"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="c4900-102">Actualización de EF Core 1,0 RC2 a RTM</span><span class="sxs-lookup"><span data-stu-id="c4900-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="c4900-103">En este artículo se proporcionan instrucciones para mover una aplicación compilada con los paquetes de RC2 a 1.0.0 RTM.</span><span class="sxs-lookup"><span data-stu-id="c4900-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="c4900-104">Versiones de paquetes</span><span class="sxs-lookup"><span data-stu-id="c4900-104">Package Versions</span></span>

<span data-ttu-id="c4900-105">Los nombres de los paquetes de nivel superior que se instalarían normalmente en una aplicación no cambiaban entre RC2 y RTM.</span><span class="sxs-lookup"><span data-stu-id="c4900-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="c4900-106">**Debe actualizar los paquetes instalados a las versiones de RTM:**</span><span class="sxs-lookup"><span data-stu-id="c4900-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="c4900-107">Los paquetes en tiempo de ejecución (por ejemplo, `Microsoft.EntityFrameworkCore.SqlServer` ) cambiaron de `1.0.0-rc2-final` a `1.0.0` .</span><span class="sxs-lookup"><span data-stu-id="c4900-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="c4900-108">El `Microsoft.EntityFrameworkCore.Tools` paquete cambió de `1.0.0-preview1-final` a `1.0.0-preview2-final` .</span><span class="sxs-lookup"><span data-stu-id="c4900-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="c4900-109">Tenga en cuenta que las herramientas siguen siendo versiones preliminares.</span><span class="sxs-lookup"><span data-stu-id="c4900-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="c4900-110">Es posible que las migraciones existentes necesiten maxLength agregadas</span><span class="sxs-lookup"><span data-stu-id="c4900-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="c4900-111">En RC2, la definición de columna en una migración `table.Column<string>(nullable: true)` se parecía y se buscó la longitud de la columna en algunos metadatos que almacenamos en el código subyacente a la migración.</span><span class="sxs-lookup"><span data-stu-id="c4900-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="c4900-112">En RTM, la longitud se incluye ahora en el código con scaffolding `table.Column<string>(maxLength: 450, nullable: true)` .</span><span class="sxs-lookup"><span data-stu-id="c4900-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="c4900-113">Las migraciones existentes con scaffolding antes de usar RTM no tendrán el `maxLength` argumento especificado.</span><span class="sxs-lookup"><span data-stu-id="c4900-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="c4900-114">Esto significa que se utilizará la longitud máxima admitida por la base de datos ( `nvarchar(max)` en SQL Server).</span><span class="sxs-lookup"><span data-stu-id="c4900-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="c4900-115">Esto puede ser adecuado para algunas columnas, pero las columnas que forman parte de una clave, clave externa o índice deben actualizarse para incluir una longitud máxima.</span><span class="sxs-lookup"><span data-stu-id="c4900-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="c4900-116">Por Convención, 450 es la longitud máxima utilizada para las claves, las claves externas y las columnas indizadas.</span><span class="sxs-lookup"><span data-stu-id="c4900-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="c4900-117">Si ha configurado explícitamente una longitud en el modelo, debe utilizar esa longitud en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c4900-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="c4900-118">ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="c4900-118">ASP.NET Identity</span></span>

<span data-ttu-id="c4900-119">Este cambio afecta a los proyectos que usan ASP.NET Identity y que se crearon a partir de una plantilla de proyecto anterior a RTM.</span><span class="sxs-lookup"><span data-stu-id="c4900-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="c4900-120">La plantilla de proyecto incluye una migración que se usa para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c4900-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="c4900-121">Esta migración se debe editar para especificar una longitud máxima de `256` para las columnas siguientes.</span><span class="sxs-lookup"><span data-stu-id="c4900-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="c4900-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="c4900-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="c4900-123">NOMBRE</span><span class="sxs-lookup"><span data-stu-id="c4900-123">Name</span></span>
  * <span data-ttu-id="c4900-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="c4900-124">NormalizedName</span></span>
* <span data-ttu-id="c4900-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="c4900-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="c4900-126">Correo electrónico</span><span class="sxs-lookup"><span data-stu-id="c4900-126">Email</span></span>
  * <span data-ttu-id="c4900-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="c4900-127">NormalizedEmail</span></span>
  * <span data-ttu-id="c4900-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="c4900-128">NormalizedUserName</span></span>
  * <span data-ttu-id="c4900-129">UserName</span><span class="sxs-lookup"><span data-stu-id="c4900-129">UserName</span></span>

<span data-ttu-id="c4900-130">Si no se realiza este cambio, se producirá la siguiente excepción cuando la migración inicial se aplique a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="c4900-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="c4900-131">.NET Core: Quite "Imports" en project.jsen</span><span class="sxs-lookup"><span data-stu-id="c4900-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="c4900-132">Si tenía como destino .NET Core con RC2, necesitaba agregar `imports` a project.jscomo solución temporal para algunas de EF Core dependencias de que no admiten .net Standard.</span><span class="sxs-lookup"><span data-stu-id="c4900-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="c4900-133">Ahora se pueden quitar.</span><span class="sxs-lookup"><span data-stu-id="c4900-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> <span data-ttu-id="c4900-134">A partir de la versión 1,0 RTM, el [SDK de .net Core](https://www.microsoft.com/net/download/core) ya no admite `project.json` ni desarrolla aplicaciones de .net Core con Visual Studio 2015.</span><span class="sxs-lookup"><span data-stu-id="c4900-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="c4900-135">Se recomienda [migrar de project.json a csproj](/dotnet/articles/core/migration/).</span><span class="sxs-lookup"><span data-stu-id="c4900-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="c4900-136">Si usa Visual Studio, se recomienda que actualice a [visual studio 2017](https://www.visualstudio.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c4900-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="c4900-137">UWP: agregar redirecciones de enlace</span><span class="sxs-lookup"><span data-stu-id="c4900-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="c4900-138">Al intentar ejecutar los comandos EF en los proyectos de Plataforma universal de Windows (UWP) se produce el siguiente error:</span><span class="sxs-lookup"><span data-stu-id="c4900-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="c4900-139">Debe agregar manualmente redirecciones de enlace al proyecto de UWP.</span><span class="sxs-lookup"><span data-stu-id="c4900-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="c4900-140">Cree un archivo denominado `App.config` en la carpeta raíz del proyecto y agregue redireccionamientos a las versiones de ensamblado correctas.</span><span class="sxs-lookup"><span data-stu-id="c4900-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

```xml
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
