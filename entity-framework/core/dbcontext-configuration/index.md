---
title: 'Duración, configuración e inicialización de DbContext: EF Core'
description: Patrones para crear y administrar instancias de DbContext con o sin inserción de dependencias
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 93d5942fbc81ee0ae9aeff0c5c8b9e20b160d512
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635397"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a>Duración, configuración e inicialización de DbContext

En este artículo se muestran los patrones básicos para la inicialización y configuración de una instancia de <xref:Microsoft.EntityFrameworkCore.DbContext>.

## <a name="the-dbcontext-lifetime"></a>La duración de DbContext

La duración de `DbContext` comienza cuando se crea la instancia y finaliza cuando la instancia se [elimina](/dotnet/standard/garbage-collection/unmanaged). Una instancia de `DbContext` está diseñada para usarse para una _única_ [unidad de trabajo](https://www.martinfowler.com/eaaCatalog/unitOfWork.html). Esto significa que la duración de una instancia de `DbContext` suele ser muy breve.

> [!TIP]
> Por citar a Martin Fowler, del vínculo anterior, "una unidad de trabajo hace un seguimiento de todas las acciones que realiza durante una transacción comercial que pueden afectar a la base de datos. Cuando ha terminado, determina todo lo que se debe hacer para modificar la base de datos como resultado de su trabajo".

Una unidad de trabajo típica al utilizar Entity Framework Core (EF Core) implica lo siguiente:

- Creación de una instancia de `DbContext`.
- Seguimiento de las instancias de entidad por el contexto. Seguimiento de las entidades mediante
  - [devolución desde una consulta](xref:core/querying/tracking)
  - [adición o asociación al contexto](xref:core/saving/disconnected-entities)
- Se realizan cambios en las entidades sometidas a seguimiento según sea necesario para implementar la regla empresarial.
- Se llama a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>. EF Core detecta los cambios realizados y los escribe en la base de datos.
- Se elimina la instancia de `DbContext`.

> [!IMPORTANT]
>
> - Es muy importante eliminar <xref:Microsoft.EntityFrameworkCore.DbContext> tras su uso. De este modo, se garantiza que se liberen los recursos no administrados y que se anule el registro de todos los eventos u otros enlaces para evitar pérdidas de memoria en caso de que se siga haciendo referencia a la instancia.
> - [DbContext **no es seguro para subprocesos**](#avoiding-dbcontext-threading-issues). No comparta contextos entre subprocesos. Asegúrese de [esperar](/dotnet/csharp/language-reference/operators/await) todas las llamadas asincrónicas antes de continuar usando la instancia de contexto.
> - Un objeto <xref:System.InvalidOperationException> generado por código de EF Core puede poner el contexto en un estado irrecuperable. Estas excepciones indican un error del programa y no están diseñadas para recuperarse.

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a>DbContext en la inserción de dependencias para ASP.NET Core

En muchas aplicaciones web, cada solicitud HTTP corresponde a una sola unidad de trabajo. Esto hace que vincular la vida útil del contexto a la de la solicitud sea un buen valor predeterminado para las aplicaciones web.

Las aplicaciones de ASP.NET Core se [configuran mediante la inserción de dependencias](/aspnet/core/fundamentals/startup). EF Core se puede agregar a esta configuración mediante <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> en el método [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) de `Startup.cs`. Por ejemplo:

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            
            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

En este ejemplo se registra una subclase `DbContext` denominada `ApplicationDbContext` como un servicio con ámbito en el proveedor de servicios de aplicación de ASP.NET Core (también conocido como contenedor de inserción de dependencias). El contexto se configura para utilizar el proveedor de base de datos de SQL Server y leerá la cadena de conexión de la configuración de ASP.NET Core. Por lo general, no importa _dónde_ en `ConfigureServices` se realiza la llamada a `AddDbContext`.

La clase `ApplicationDbContext` debe exponer un constructor público con un parámetro `DbContextOptions<ApplicationDbContext>`. Así es cómo se pasa la configuración de contexto de `AddDbContext` a `DbContext`. Por ejemplo:

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

`ApplicationDbContext` se puede usar en controladores de ASP.NET Core u otros servicios a través de la inserción de constructores. Por ejemplo:

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

El resultado final es una instancia de `ApplicationDbContext` creada para cada solicitud y que se pasa al controlador para realizar una unidad de trabajo antes de que se elimine cuando finalice la solicitud.

Lea más adelante en este artículo para obtener más información sobre las opciones de configuración. Además, consulte [Inicio de la aplicación en ASP.NET Core](/aspnet/core/fundamentals/startup) e [Inserción de dependencias en ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) para obtener más información sobre la configuración y la inserción de dependencias en ASP.NET Core.

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a>Inicialización de DbContext simple con "New"

Las instancias de `DbContext` se pueden construir de la manera normal de .NET, por ejemplo, con `new` en C#. La configuración se puede realizar invalidando el método `OnConfiguring` o pasando opciones al constructor. Por ejemplo:

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

Este patrón también facilita el paso de la configuración, como la cadena de conexión, a través del constructor `DbContext`. Por ejemplo:

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

Como alternativa, `DbContextOptionsBuilder` se puede usar para crear un objeto `DbContextOptions` que se pasa a continuación al constructor `DbContext`. Esto permite que el elemento `DbContext` configurado para la inserción de dependencias también se construya explícitamente. Por ejemplo, al usar el elemento `ApplicationDbContext` definido para aplicaciones web de ASP.NET Core que vimos anteriormente:

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

`DbContextOptions` se puede crear y se puede llamar al constructor explícitamente:

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a>Uso de un generador de DbContext (por ejemplo, para Blazor)

Algunos tipos de aplicaciones (por ejemplo, [ASP.NET Core Blazor](/aspnet/core/blazor/)) utilizan la inserción de dependencias, pero no crean un ámbito de servicio que se alinee con la duración de `DbContext` deseada. Incluso en los casos en los que exista una alineación, es posible que la aplicación tenga que realizar varias unidades de trabajo en este ámbito. Por ejemplo, varias unidades de trabajo en una única solicitud HTTP.

En estos casos, se puede usar <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> para registrar un generador para la creación de instancias de `DbContext`. Por ejemplo:

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

La clase `ApplicationDbContext` debe exponer un constructor público con un parámetro `DbContextOptions<ApplicationDbContext>`. Este es el mismo patrón que se usa en la sección de ASP.NET Core tradicional anterior.

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

El generador de `DbContextFactory` se puede utilizar en otros servicios a través de la inserción de constructores. Por ejemplo:

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

A continuación, el generador insertado se puede usar para construir instancias de DbContext en el código del servicio. Por ejemplo:

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

Tenga en cuenta que las instancias de `DbContext` creadas de este modo **no** están administradas por el proveedor de servicios de la aplicación y, por lo tanto, la aplicación debe eliminarlas.

Consulte [Blazor Server de ASP.NET Core con Entity Framework Core (EF Core)](/aspnet/core/blazor/blazor-server-ef-core) para obtener más información sobre el uso de EF Core con Blazor.

## <a name="dbcontextoptions"></a>DbContextOptions

El punto inicial de toda la configuración de `DbContext` es <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>. Hay tres maneras de obtener este generador:

- En `AddDbContext` y métodos relacionados
- En `OnConfiguring`
- Construido explícitamente con `new`

En las secciones anteriores se muestran ejemplos de cada una de ellas. Se puede aplicar la misma configuración independientemente de la procedencia del constructor. Además, siempre se llama a `OnConfiguring` independientemente de cómo se construya el contexto. Esto significa que `OnConfiguring` se puede usar para realizar una configuración adicional incluso cuando se utiliza `AddDbContext`.

### <a name="configuring-the-database-provider"></a>Configuración del proveedor de base de datos

Cada instancia de `DbContext` debe estar configurada para usar un único proveedor de bases de datos. (Se pueden usar diferentes instancias de un subtipo `DbContext` con diferentes proveedores de bases de datos, pero una sola instancia solo debe usar uno). Un proveedor de base de datos se configura mediante una llamada de `Use*`" específica. Por ejemplo, para usar el proveedor de base de datos de SQL Server:

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

Estos métodos de `Use*`" son métodos de extensión implementados por el proveedor de bases de datos. Esto significa que el paquete NuGet del proveedor de bases de datos debe estar instalado para poder usar el método de extensión.

> [!TIP]
> Los proveedores de bases de datos de EF Core hacen un uso extensivo de [métodos de extensión](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods). Si el compilador indica que no se puede encontrar un método, asegúrese de que el paquete NuGet del proveedor está instalado y de que tiene `using Microsoft.EntityFrameworkCore;` en el código.

En la tabla siguiente se incluyen ejemplos de proveedores de bases de datos comunes.

| Sistema de base de datos              | Ejemplo de configuración                         | Detección de
|:-----------------------------|-----------------------------------------------|--------------
| SQL Server o Azure SQL      | `.UseSqlServer(connectionString)`             | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| Azure Cosmos DB              | `.UseCosmos(connectionString, databaseName)`  | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| SQLite                       | `.UseSqlite(connectionString)`                | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| Base de datos en memoria de EF Core   | `.UseInMemoryDatabase(databaseName)`          | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| PostgreSQL*                  | `.UseNpgsql(connectionString)`                | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| MySQL/MariaDB*               | `.UseMySql((connectionString)`                | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| Oracle*                      | `.UseOracle(connectionString)`                | [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

*Microsoft no entrega estos proveedores de bases de datos. Vea [Proveedores de bases de datos](xref:core/providers/index) para obtener más información acerca de los proveedores de bases de datos.

> [!WARNING]
> La base de datos en memoria de EF Core no está diseñada para uso en producción. Además, es posible que no sea la mejor opción incluso para las pruebas. Consulte [Pruebas de código que usa EF Core](xref:core/testing/index) para obtener más información.

Consulte [Cadenas de conexión](xref:core/miscellaneous/connection-strings) para obtener más información sobre el uso de cadenas de conexión con EF Core.

La configuración opcional específica del proveedor de bases de datos se realiza en un generador adicional específico del proveedor. Por ejemplo, el uso de <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> para configurar reintentos para la resistencia de la conexión al conectarse a Azure SQL:

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> Se usa el mismo proveedor de bases de datos para SQL Server y Azure SQL. Sin embargo, se recomienda usar la [resistencia de la conexión](xref:core/miscellaneous/connection-resiliency) para la conexión a SQL Azure.

Vea [Proveedores de bases de datos](xref:core/providers/index) para obtener más información sobre la configuración específica del proveedor.

### <a name="other-dbcontext-configuration"></a>Otra configuración de DbContext

Otra configuración de `DbContext` se puede encadenar antes o después (no hay diferencia) de la llamada de `Use*`. Por ejemplo, para activar el registro de datos confidenciales:

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

En la tabla siguiente se incluyen ejemplos de métodos comunes a los que se llama en `DbContextOptionsBuilder`.

| Método DbContextOptionsBuilder                                                             | Qué hace                                                | Más información
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | Establece el comportamiento de seguimiento predeterminado para las consultas.              | [Comportamiento del seguimiento de las consultas](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | Una manera sencilla de obtener registros de EF Core (EF Core 5.0 y versiones posteriores)    | [Registro, eventos y diagnósticos](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | Registra un generador de `Micrsofot.Extensions.Logging`.         | [Registro, eventos y diagnósticos](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | Incluye datos de aplicación en excepciones y registro.         | [Registro, eventos y diagnósticos](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | Errores de consulta más detallados (a costa del rendimiento).  | [Registro, eventos y diagnósticos](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | Omite o inicia advertencias y otros eventos.               | [Registro, eventos y diagnósticos](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | Registra los interceptores de EF Core.                              | [Registro, eventos y diagnósticos](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | Usa servidores proxy dinámicos para la carga diferida.                        | [Carga diferida](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | Usa servidores proxy dinámicos para el seguimiento de cambios.                     | Próximamente...

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> y <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> son métodos de extensión de los paquetes NuGet [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/). Este tipo de llamada de ".UseSomething()" es la forma recomendada de configurar o usar las extensiones de EF Core incluidas en otros paquetes.

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a>`DbContextOptions` frente a `DbContextOptions<TContext>`

La mayoría de las subclases `DbContext` que aceptan `DbContextOptions` deben usar la variación `DbContextOptions<TContext>` [genérica](/dotnet/csharp/programming-guide/generics/). Por ejemplo:

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

Esto garantiza que las opciones correctas para el subtipo `DbContext` específico se resuelvan a partir de la inserción de dependencias, incluso cuando se registran varios subtipos `DbContext`.

> [!TIP]
> No es necesario que el DbContext esté sellado, pero el sellado es el procedimiento recomendado para las clases que no están diseñadas para heredarse.

Sin embargo, si el subtipo `DbContext` va a heredarse, debe exponer un constructor protegido que tome un elemento `DbContextOptions` no genérico. Por ejemplo:

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

Esto permite que varias subclases concretas llamen a este constructor base mediante sus diferentes instancias de `DbContextOptions<TContext>` genéricas. Por ejemplo:

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

Observe que este es exactamente el mismo patrón que al heredar de `DbContext` directamente. Es decir, el propio constructor `DbContext` acepta un elemento `DbContextOptions` no genérico por esta razón.

Una subclase `DbContext` de la que se va a crear una instancia y se puede heredar debe exponer ambas formas de constructor. Por ejemplo:

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a>Configuración de DbContext en tiempo de diseño

Es necesario que herramientas en tiempo de diseño de EF Core, como aquellas para [migraciones de EF Core](xref:core/managing-schemas/migrations/index), puedan detectar y crear una instancia de trabajo de un tipo `DbContext` para recopilar detalles sobre los tipos de entidad de la aplicación y cómo se asignan a un esquema de base de datos. Este proceso puede ser automático siempre y cuando la herramienta pueda crear fácilmente el `DbContext` de tal forma que se configure de manera similar a como se configuraría en tiempo de ejecución.

Aunque cualquier patrón que proporcione la información de configuración necesaria al `DbContext` puede funcionar en tiempo de ejecución, las herramientas que requieren que se use un `DbContext` en tiempo de diseño solo pueden funcionar con un número de patrones limitado. Estos se tratan con más detalle en [Creación de contexto en tiempo de diseño](xref:core/cli/dbcontext-creation).

## <a name="avoiding-dbcontext-threading-issues"></a>Evitar problemas con el subprocesamiento de DbContext

Entity Framework Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de `DbContext`. Esto incluye la ejecución en paralelo de consultas asincrónicas y cualquier uso simultáneo explícito desde varios subprocesos. Por tanto, espere siempre llamadas asincrónicas de inmediato mediante el operador `await` o use instancias de `DbContext` independientes para operaciones que se ejecuten en paralelo.

Cuando EF Core detecte un intento de usar una instancia de `DbContext` simultáneamente, verá un elemento `InvalidOperationException` con un mensaje como este:

> Se inició una segunda operación en este contexto antes de que se completara una operación anterior. Esto se debe normalmente a distintos subprocesos que usan la misma instancia de DbContext. Sin embargo, no se garantiza que los miembros de instancia sean seguros para subprocesos.

Cuando el acceso simultáneo no se detecta, puede dar lugar a un comportamiento indefinido, a bloqueos de la aplicación y a daño en los datos.

Hay errores comunes que pueden dar lugar accidentalmente a un acceso simultáneo en la misma instancia de `DbContext`:

### <a name="asynchronous-operation-pitfalls"></a>Errores de operaciones asincrónicas

Los métodos asincrónicos habilitan EF Core para iniciar operaciones con acceso a la base de datos sin bloqueos. Pero si un llamador no espera a que finalice uno de estos métodos y sigue realizando otras operaciones en el `DbContext`, el estado del `DbContext` puede estar (y muy probablemente lo estará) dañado.

Espere siempre métodos asincrónicos de EF Core de inmediato.

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a>Uso compartido implícito de instancias de DbContext mediante la inserción de dependencias

El método de extensión [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) registra tipos de `DbContext` con una [duración de ámbito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) de forma predeterminada.

Está protegido frente a problemas de acceso simultáneo en la mayoría de las aplicaciones ASP.NET Core, ya que solo hay un subproceso ejecutando cada solicitud de cliente en un momento dado, y cada solicitud obtiene un ámbito de inserción de dependencias independiente (y, por tanto, una instancia de `DbContext` independiente). Para el modelo de hospedaje de Blazor Server, se usa una solicitud lógica para mantener el circuito de usuario de Blazor y, por tanto, solo hay disponible una instancia de DbContext con ámbito por circuito de usuario si se usa el ámbito de inserción predeterminado.

Cualquier código que ejecute explícitamente varios subprocesos en paralelo debe garantizar que no se tenga nunca acceso a las instancias de `DbContext` simultáneamente.

Con la inserción de dependencias, esto se puede lograr registrando el contexto como con ámbito y creando ámbitos (mediante `IServiceScopeFactory`) para cada subproceso, o bien registrando el `DbContext` como transitorio (mediante la sobrecarga de `AddDbContext`, que toma un parámetro `ServiceLifetime`).

## <a name="more-reading"></a>Más lectura

- Lea [Inserción de dependencias](/aspnet/core/fundamentals/dependency-injection) para obtener más información sobre el uso de la inserción de dependencias.
- Lea [Pruebas](xref:core/testing/index) para obtener más información.
