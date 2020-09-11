---
title: Novedades de EF6
description: Novedades de Entity Framework 6
author: divega
ms.date: 09/12/2019
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
uid: ef6/what-is-new/index
ms.openlocfilehash: 71854e0a7009b35742a70724210708a284f50a3d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619759"
---
# <a name="whats-new-in-ef6"></a>Novedades de EF6

Se recomienda encarecidamente usar la versión más reciente de Entity Framework para asegurarse de obtener las últimas características y la mayor estabilidad.
Pero es posible que deba usar una versión anterior o que quiera experimentar con las nuevas mejoras de la última versión preliminar.
Para instalar versiones concretas de EF, vea [Get Entity Framework](xref:ef6/fundamentals/install) (Obtener Entity Framework).

## <a name="ef-640"></a>EF 6.4.0

El runtime de EF 6.4.0 para NuGet se publicó en diciembre de 2019. El objetivo principal de EF 6.4 es perfeccionar las características y los escenarios que se ofrecieron en EF 6.3. Consulte la [lista de correcciones importantes](https://github.com/dotnet/ef6/milestone/14?closed=1) en Github.

## <a name="ef-630"></a>EF 6.3.0

El runtime de EF 6.3.0 para NuGet se publicó en septiembre de 2019. El objetivo principal de esta versión era facilitar la migración de las aplicaciones existentes que usan EF 6 a .NET Core 3.0. La comunidad también ha contribuido con varias correcciones de errores y mejoras. Consulte los problemas cerrados en cada [hito](https://github.com/aspnet/EntityFramework6/milestones?state=closed) de la versión 6.3.0 para información detallada. Estos son algunos de los más importantes:

- Compatibilidad con .NET Core 3.0.
  - El paquete EntityFramework ahora tiene como destino .NET Standard 2.1 además de .NET Framework 4.x.
  - Esto significa que EF 6.3 es multiplataforma y se admite en otros sistemas operativos además de Windows, como Linux y macOS.
  - Se han vuelto a escribir los comandos de migración para ejecutarlos fuera de proceso y que trabajen con proyectos de estilo de SDK.
- Compatibilidad con HierarchyId de SQL Server.
- Compatibilidad mejorada con PackageReference de Roslyn y NuGet.
- Se agregó la utilidad `ef6.exe` para habilitar, agregar, generar scripts y aplicar migraciones desde los ensamblados. Reemplaza a `migrate.exe`.

### <a name="ef-designer-support"></a>Compatibilidad con el diseñador de EF

Actualmente no se admite el uso del diseñador de EF directamente en proyectos de .NET Core o .NET Standard o en un proyecto de .NET Framework de estilo SDK. 

Puede solucionar esta limitación agregando el archivo EDMX y las clases generadas para las entidades y DbContext como archivos vinculados a un proyecto de .NET Core 3.0 o .NET Standard 2.1 en la misma solución.

Los archivos vinculados tendrían el aspecto siguiente:

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

Tenga en cuenta que el archivo EDMX está vinculado con la acción de compilación EntityDeploy. Se trata de una tarea especial de MSBuild (ahora incluida en el paquete EF 6.3) que se encarga de agregar el modelo EF en el ensamblado de destino como recursos incrustados (o de copiarlo como archivos en la carpeta de salida, en función de la configuración de procesamiento de artefactos de metadatos en EDMX). Para más información sobre cómo configurar esta configuración, consulte nuestro [ejemplo de .NET Core de EDMX](https://aka.ms/EdmxDotNetCoreSample).

Advertencia: Asegúrese de que el estilo antiguo (es decir, no SDK) del proyecto de .NET Framework que define el archivo. edmx "real" se incluye _antes_ del proyecto que define el vínculo dentro del archivo. sln. De lo contrario, al abrir el archivo. edmx en el diseñador, verá el mensaje de error "The Entity Framework is not available in the target framework currently specified for the project. You can change the target framework of the project or edit the model in the XmlEditor" ("Entity Framework no está disponible en la plataforma de destino especificada actualmente para el proyecto. Puede cambiar la plataforma de destino del proyecto o editar el modelo en XmlEditor").

## <a name="past-releases"></a>Versiones anteriores

La página [Versiones anteriores](xref:ef6/what-is-new/past-releases) contiene un archivo de todas las versiones anteriores de EF y de las características principales incluidas en cada versión.
