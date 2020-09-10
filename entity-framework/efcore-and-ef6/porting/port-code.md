---
title: 'Portabilidad de EF6 a EF Core: portabilidad de un modelo basado en código - EF'
description: Información específica acerca de cómo migrar una aplicación de modelo basada en código de Entity Framework 6 a Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: d3920e6132576e3a93dd0ffb9dac1412b6e511aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619616"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>Portabilidad de un modelo basado en código de EF6 a EF Core

Si ha leído todas las advertencias y está a punto para realizar la portabilidad, estas son algunas instrucciones que le ayudarán a empezar.

## <a name="install-ef-core-nuget-packages"></a>Instalación de los paquetes NuGet de EF Core

Para usar EF Core, instale el paquete NuGet correspondiente al proveedor de base de datos que quiera usar. Por ejemplo, cuando el destino es SQL Server, tendría que instalar `Microsoft.EntityFrameworkCore.SqlServer`. Para obtener más información, vea [Proveedores de bases de datos](xref:core/providers/index).

Si tiene previsto usar migraciones, también debe instalar el paquete `Microsoft.EntityFrameworkCore.Tools`.

No hay problema por dejar el paquete NuGet de EF6 (EntityFramework) instalado, ya que EF Core y EF6 se pueden usar en paralelo en la misma aplicación. Sin embargo, si no va a usar EF6 en ninguna de las áreas de la aplicación, la desinstalación del paquete le ayudará a dar errores de compilación en fragmentos de código que requieren atención.

## <a name="swap-namespaces"></a>Intercambio de espacios de nombres

La mayoría de las API que se usan en EF6 se encuentran en el espacio de nombres `System.Data.Entity` (y los subespacios de nombres relacionados). El primer cambio de código consiste en cambiar al espacio de nombres `Microsoft.EntityFrameworkCore`. Normalmente, empezará con el archivo de código de contexto derivado y, después, avanzará desde allí y solucionará los errores de compilación a medida que aparezcan.

## <a name="context-configuration-connection-etc"></a>Configuración del contexto (conexión, etc.)

Como se describe en [Asegurarse de que EF Core funcionará para la aplicación](xref:efcore-and-ef6/porting/index), la detección de la base de datos a la que se va a conectar EF Core tiene menos secretos. Tendrá que reemplazar el método `OnConfiguring` en el contexto derivado y usar la API específica del proveedor de base de datos para configurar la conexión a la base de datos.

La mayoría de las aplicaciones EF6 almacenan la cadena de conexión en el archivo `App/Web.config` de las aplicaciones. En EF Core, esta cadena de conexión se lee mediante la API `ConfigurationManager`. Es posible que tenga que agregar una referencia al ensamblado del marco `System.Configuration` para poder usar esta API.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a>Actualización del código

En este punto, es cuestión de solucionar los errores de compilación y de revisar el código para ver si los cambios de comportamiento le afectarán.

## <a name="existing-migrations"></a>Migraciones existentes

Realmente no existe una manera viable de realizar la portabilidad de las migraciones de EF6 existentes a EF Core.

Si es posible, es mejor suponer que todas las migraciones anteriores de EF6 se han aplicado a la base de datos y, después, iniciar la migración del esquema desde ese punto mediante EF Core. Para ello, use el comando `Add-Migration` para agregar una migración una vez que el modelo se haya trasladado a EF Core. Después, podría quitar todo el código de los métodos `Up` y `Down` de la migración con scaffolding. Las migraciones posteriores se compararán con el modelo cuando se haya aplicado scaffolding a la migración inicial.

## <a name="test-the-port"></a>Prueba del puerto

El hecho de que la aplicación se compile no significa que se haya trasladado correctamente a EF Core. Tendrá que probar todas las áreas de la aplicación para asegurarse de que ninguno de los cambios de comportamiento afecte de forma negativa a la aplicación.
