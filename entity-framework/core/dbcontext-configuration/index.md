---
title: 'Configuración de un DbContext: EF Core'
description: Estrategias para configurar elementos DbContext con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431149"
---
# <a name="configuring-a-dbcontext"></a>Configuración de un DbContext

En este artículo se muestran los patrones básicos para configurar un `DbContext` a través de un elemento `DbContextOptions` para conectarse a una base de datos mediante un proveedor de EF Core específico y comportamientos opcionales.

## <a name="design-time-dbcontext-configuration"></a>Configuración de DbContext en tiempo de diseño

Es necesario que herramientas en tiempo de diseño de EF Core como las [migraciones](xref:core/managing-schemas/migrations/index) puedan detectar y crear una instancia de trabajo de un tipo `DbContext` para recopilar detalles sobre los tipos de entidad de la aplicación y cómo se asignan a un esquema de base de datos. Este proceso puede ser automático siempre y cuando la herramienta pueda crear fácilmente el `DbContext` de tal forma que se configure de manera similar a como se configuraría en tiempo de ejecución.

Aunque cualquier patrón que proporcione la información de configuración necesaria al `DbContext` puede funcionar en tiempo de ejecución, las herramientas que requieren que se use un `DbContext` en tiempo de diseño solo pueden funcionar con un número de patrones limitado. Estos se tratan con más detalle en la sección [Creación de contexto en tiempo de diseño](xref:core/cli/dbcontext-creation).

## <a name="configuring-dbcontextoptions"></a>Configuring DbContextOptions

`DbContext` debe tener una instancia de `DbContextOptions` para realizar cualquier trabajo. La instancia de `DbContextOptions` contiene información de configuración como la siguiente:

- El proveedor de base de datos que se va a usar, normalmente seleccionado invocando un método como `UseSqlServer` o `UseSqlite`. Estos métodos de extensión requieren el paquete de proveedor correspondiente, como `Microsoft.EntityFrameworkCore.SqlServer` o `Microsoft.EntityFrameworkCore.Sqlite`. Los métodos se definen en el espacio de nombres `Microsoft.EntityFrameworkCore`.
- Cualquier cadena de conexión o identificador de la instancia de base de datos necesarios, normalmente pasados como argumento al método de selección de proveedor mencionado anteriormente
- Cualquier selector de comportamiento opcional de nivel de proveedor, normalmente también encadenado dentro de la llamada al método de selección de proveedor
- Cualquier selector de comportamiento de EF Core general, normalmente encadenado después o antes del método de selector de proveedor

En el ejemplo siguiente se configura el elemento `DbContextOptions` para usar el proveedor de SQL Server, una conexión contenida en la variable `connectionString`, un tiempo de espera de comando de nivel de proveedor y un selector de comportamiento de EF Core que hace que todas las consultas ejecutadas en el `DbContext` sean de [no seguimiento](xref:core/querying/tracking#no-tracking-queries) de forma predeterminada:

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> Los métodos de selector de proveedor y otros métodos de selector de comportamiento mencionados anteriormente son métodos de extensión en `DbContextOptions` o clases de opciones específicas del proveedor. Para tener acceso a estos métodos de extensión, es posible que necesite tener un espacio de nombres (normalmente `Microsoft.EntityFrameworkCore`) en el ámbito, así como incluir dependencias de paquetes adicionales en el proyecto.

El elemento `DbContextOptions` se puede proporcionar al `DbContext` invalidando el método `OnConfiguring` o externamente a través de un argumento del constructor.

Si se usan ambos, `OnConfiguring` se aplicará en último lugar y podrá sobrescribir opciones proporcionadas al argumento del constructor.

### <a name="constructor-argument"></a>Argumento del constructor

El constructor simplemente puede aceptar un elemento `DbContextOptions` como se indica a continuación:

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> El constructor base de DbContext también acepta la versión no genérica de `DbContextOptions`, pero su uso no se recomienda para aplicaciones con varios tipos de contexto.

Ahora la aplicación puede pasar el elemento `DbContextOptions` al crear una instancia de un contexto, como se indica a continuación:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

También puede inicializar el elemento `DbContextOptions` dentro del propio contexto. Aunque puede usar esta técnica para la configuración básica, normalmente seguirá siendo necesario que obtenga determinados detalles de configuración del exterior como, por ejemplo, una cadena de conexión de base de datos. Esto puede hacerse con una API de configuración o con cualquier otro medio.

Para inicializar `DbContextOptions` dentro del contexto, invalide el método `OnConfiguring` y llame a los métodos en el elemento `DbContextOptionsBuilder` proporcionado:

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

Una aplicación puede simplemente crear una instancia de este contexto sin pasar nada a su constructor:

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> Este enfoque no se presta a pruebas, a menos que las pruebas tengan como destino la base de datos completa.

### <a name="using-dbcontext-with-dependency-injection"></a>Uso de DbContext con inserción de dependencias

EF Core admite el uso de `DbContext` con un contenedor de inserción de dependencias. El tipo DbContext se puede agregar al contenedor de servicios mediante el método `AddDbContext<TContext>`.

`AddDbContext<TContext>` hará que el tipo DbContext, `TContext`, y el elemento `DbContextOptions<TContext>` correspondiente estén disponibles para la inserción desde el contenedor de servicios.

Consulte [más lectura](#more-reading) a continuación para obtener información adicional sobre la inserción de dependencias.

Incorporación del elemento `DbContext` a la inserción de dependencias:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

Para ello, es necesario agregar un [argumento del constructor](#constructor-argument) al tipo DbContext que acepte `DbContextOptions<TContext>`.

Código de contexto:

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

Código de aplicación (en ASP.NET Core):

```csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

Código de aplicación (mediante ServiceProvider directamente, menos común):

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>Evitar problemas con el subprocesamiento de DbContext

Entity Framework Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de `DbContext`. Esto incluye la ejecución en paralelo de consultas asincrónicas y cualquier uso simultáneo explícito desde varios subprocesos. Por tanto, espere siempre llamadas asincrónicas de inmediato mediante el operador `await` o use instancias de `DbContext` independientes para operaciones que se ejecuten en paralelo.

Cuando EF Core detecte un intento de usar una instancia de `DbContext` simultáneamente, verá un elemento `InvalidOperationException` con un mensaje como este:

> Se inició una segunda operación en este contexto antes de que se completara una operación anterior. Esto se debe normalmente a distintos subprocesos que usan la misma instancia de DbContext. Sin embargo, no se garantiza que los miembros de instancia sean seguros para subprocesos.

Cuando el acceso simultáneo no se detecta, puede dar lugar a un comportamiento indefinido, a bloqueos de la aplicación y a daño en los datos.

Hay errores comunes que pueden dar lugar accidentalmente a un acceso simultáneo en la misma instancia de `DbContext`:

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>Olvido de la espera de la finalización de una operación asincrónica antes de iniciar cualquier otra operación en el mismo DbContext

Los métodos asincrónicos habilitan EF Core para iniciar operaciones con acceso a la base de datos sin bloqueos. Pero si un llamador no espera a que finalice uno de estos métodos y sigue realizando otras operaciones en el `DbContext`, el estado del `DbContext` puede estar (y muy probablemente lo estará) dañado.

Espere siempre métodos asincrónicos de EF Core de inmediato.

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>Uso compartido implícito de instancias de DbContext entre varios subprocesos a través de la inserción de dependencias

El método de extensión [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) registra tipos de `DbContext` con una [duración de ámbito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) de forma predeterminada.

Está protegido frente a problemas de acceso simultáneo en la mayoría de las aplicaciones ASP.NET Core, ya que solo hay un subproceso ejecutando cada solicitud de cliente en un momento dado, y cada solicitud obtiene un ámbito de inserción de dependencias independiente (y, por tanto, una instancia de `DbContext` independiente). Para el modelo de hospedaje de Blazor Server, se usa una solicitud lógica para mantener el circuito de usuario de Blazor y, por tanto, solo hay disponible una instancia de DbContext con ámbito por circuito de usuario si se usa el ámbito de inserción predeterminado.

Cualquier código que ejecute explícitamente varios subprocesos en paralelo debe garantizar que no se tenga nunca acceso a las instancias de `DbContext` simultáneamente.

Con la inserción de dependencias, esto se puede lograr registrando el contexto como con ámbito y creando ámbitos (mediante `IServiceScopeFactory`) para cada subproceso, o bien registrando el `DbContext` como transitorio (mediante la sobrecarga de `AddDbContext`, que toma un parámetro `ServiceLifetime`).

## <a name="more-reading"></a>Más lectura

- Lea [Inserción de dependencias](/aspnet/core/fundamentals/dependency-injection) para obtener más información sobre el uso de la inserción de dependencias.
- Lea [Pruebas](xref:core/testing/index) para obtener más información.
