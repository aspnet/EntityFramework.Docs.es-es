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
# <a name="design-time-services"></a>Servicios en tiempo de diseño

Algunos servicios usados por las herramientas solo se usan en tiempo de diseño. Estos servicios se administran de forma independiente de los servicios en tiempo de ejecución de EF Core para evitar que se implementen con la aplicación. Para invalidar uno de estos servicios (por ejemplo, el servicio para generar archivos de migración), agregue una implementación de `IDesignTimeServices` al proyecto de inicio.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>Referencia a Microsoft. EntityFrameworkCore. Design

Microsoft. EntityFrameworkCore. Design es un paquete de DevelopmentDependency. Esto significa que la dependencia no fluye de manera transitiva en otros proyectos y que, de forma predeterminada, no puede hacer referencia a sus tipos.

Para hacer referencia a sus tipos e invalidar los servicios en tiempo de diseño, actualice los metadatos del elemento PackageReference en el archivo del proyecto.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Si se hace referencia al paquete de forma transitiva a través de Microsoft. EntityFrameworkCore. Tools, tendrá que agregar un PackageReference explícito al paquete y cambiar sus metadatos.

## <a name="list-of-services"></a>Lista de servicios

A continuación se muestra una lista de los servicios en tiempo de diseño.

Servicio                                                                              | Descripción
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | Genera el código para las anotaciones del modelo correspondientes.
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | Ayuda a generar código de C#.
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | Palabras singular y plural nombres.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | Genera código para una migración.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | La clase principal para administrar los archivos de migración.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | Crea un modelo de base de datos a partir de una base de datos.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | Genera código para un modelo.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | Genera código de configuración.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | La clase principal para la scaffolding de modelos con ingeniería inversa.

## <a name="using-services"></a>Usar servicios

Estos servicios también pueden ser útiles para crear sus propias herramientas. Por ejemplo, si desea automatizar parte del flujo de trabajo de su tiempo de diseño.

Puede crear un proveedor de servicios que contenga estos servicios mediante los métodos de extensión AddEntityFrameworkDesignTimeServices y AddDbContextDesignTimeServices.

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
