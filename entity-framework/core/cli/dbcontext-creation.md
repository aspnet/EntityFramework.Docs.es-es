---
title: 'Creación de DbContext en tiempo de diseño: EF Core'
description: Estrategias para crear un DbContext en tiempo de diseño con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dbcontext-creation
ms.openlocfilehash: 144ed26dcf605dc29d53519ad2ea9cea58fb4e44
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431361"
---
# <a name="design-time-dbcontext-creation"></a>Creación de DbContext en tiempo de diseño

Algunos de los comandos de herramientas de EF Core (por ejemplo, los comandos [Migrations][1] ) requieren `DbContext` que se cree una instancia derivada en tiempo de diseño para recopilar detalles sobre los tipos de entidad de la aplicación y cómo se asignan a un esquema de base de datos. En la mayoría de los casos, es conveniente que el `DbContext` creado por tanto se configure de forma similar a como se [configuraría en tiempo de ejecución][2].

Hay varias maneras en las que las herramientas intentan crear `DbContext` :

## <a name="from-application-services"></a>De servicios de aplicación

Si el proyecto de inicio usa el host de [Web ASP.net Core][3] o el [host genérico de .net Core][4], las herramientas intentan obtener el objeto DbContext del proveedor de servicios de la aplicación.

En primer lugar, las herramientas intentan obtener el proveedor de servicios invocando `Program.CreateHostBuilder()` , llamando a `Build()` y, a continuación, accediendo a la `Services` propiedad.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> Cuando se crea una nueva aplicación de ASP.NET Core, este enlace se incluye de forma predeterminada.

El `DbContext` propio y las dependencias de su constructor deben registrarse como servicios en el proveedor de servicios de la aplicación. Esto se puede lograr fácilmente si se tiene [un constructor en `DbContext` que toma una instancia de `DbContextOptions<TContext>` como argumento][5] y usa el [ `AddDbContext<TContext>` método][6].

## <a name="using-a-constructor-with-no-parameters"></a>Usar un constructor sin parámetros

Si DbContext no se puede obtener del proveedor de servicios de aplicación, las herramientas buscan el `DbContext` tipo derivado dentro del proyecto. A continuación, intentan crear una instancia mediante un constructor sin parámetros. Este puede ser el constructor predeterminado si `DbContext` se configura mediante el [`OnConfiguring`][7] método.

## <a name="from-a-design-time-factory"></a>Desde un generador en tiempo de diseño

También puede indicar a las herramientas cómo crear su DbContext implementando la `IDesignTimeDbContextFactory<TContext>` interfaz: Si una clase que implementa esta interfaz se encuentra en el mismo proyecto que el derivado `DbContext` o en el proyecto de inicio de la aplicación, las herramientas omiten las otras formas de crear el dbcontext y usar en su lugar el generador en tiempo de diseño.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> Antes de EFCore 5,0 `args` , el parámetro no se usaba (vea [este problema][8]).
> Esto se corrigió en EFCore 5,0 y cualquier argumento adicional en tiempo de diseño se pasa a la aplicación a través de ese parámetro.

Un generador en tiempo de diseño puede ser especialmente útil si necesita configurar DbContext de manera diferente para el tiempo de diseño que en tiempo de ejecución, si el `DbContext` constructor toma parámetros adicionales que no están registrados en di, si no usa di en absoluto, o si por alguna razón prefiere no tener un `CreateHostBuilder` método en la clase de la aplicación ASP.net Core `Main` .

## <a name="args"></a>Args

Tanto IDesignTimeDbContextFactory. CreateDbContext como Program. CreateHostBuilder aceptan argumentos de la línea de comandos.

A partir de EF Core 5,0, puede especificar estos argumentos en las herramientas:

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

El `--` token dirige `dotnet ef` para tratar todo lo que sigue como argumento y no intentar analizarlos como opciones. Los argumentos adicionales que no use `dotnet ef` se reenvían a la aplicación.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database -Args '--environment Production'
```

***

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/dbcontext-configuration/index
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/dbcontext-configuration/index#constructor-argument
  [6]: xref:core/dbcontext-configuration/index#using-dbcontext-with-dependency-injection
  [7]: xref:core/dbcontext-configuration/index#onconfiguring
  [8]: https://github.com/dotnet/efcore/issues/8332
