---
title: 'Modelo de proveedor de Entity Framework 6: EF6'
description: El modelo de proveedor de Entity Framework 6 en Entity Framework 6
author: ajcvickers
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: 15b5443ff05b5c8704f80d4f2f83b4ed20edd1c0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063197"
---
# <a name="the-entity-framework-6-provider-model"></a>Modelo de proveedor de Entity Framework 6

El modelo de proveedor de Entity Framework permite utilizar Entity Framework con diferentes tipos de servidor de base de datos. Por ejemplo, se puede conectar un proveedor para permitir el uso de EF en Microsoft SQL Server, mientras que otro proveedor puede estar conectado a para permitir que EF se use en Microsoft SQL Server Compact Edition. Los proveedores de EF6 que somos conscientes de pueden encontrarse en la página [proveedores de Entity Framework](xref:ef6/fundamentals/providers/index) .

Algunos cambios fueron necesarios para la manera en que EF interactúa con los proveedores para permitir que EF se libere bajo una licencia de código abierto. Estos cambios requieren la regeneración de los proveedores de EF en los ensamblados de EF6 junto con los nuevos mecanismos de registro del proveedor.

## <a name="rebuilding"></a>Regeneración

Con EF6, el código principal que anteriormente formaba parte de la .NET Framework se envía ahora como ensamblados fuera de banda (OOB). Puede encontrar información sobre cómo compilar aplicaciones en EF6 en la página [actualizar aplicaciones para EF6](xref:ef6/what-is-new/upgrading-to-ef6) . También será necesario volver a generar los proveedores mediante estas instrucciones.

## <a name="provider-types-overview"></a>Información general sobre tipos de proveedor

Un proveedor de EF es realmente una colección de servicios específicos del proveedor definidos por tipos CLR que estos servicios extienden de (para una clase base) o implementan (para una interfaz). Dos de estos servicios son fundamentales y necesarios para que EF funcione en absoluto. Otros son opcionales y solo deben implementarse si se requiere una funcionalidad específica y/o las implementaciones predeterminadas de estos servicios no funcionan para el servidor de base de datos específico de destino.

## <a name="fundamental-provider-types"></a>Tipos fundamentales de proveedor

### <a name="dbproviderfactory"></a>DbProviderFactory

EF depende de tener un tipo derivado de [System. Data. Common. DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) para realizar todo el acceso a la base de datos de bajo nivel. DbProviderFactory no es realmente parte de EF, sino que es una clase en el .NET Framework que sirve un punto de entrada para los proveedores de ADO.NET que puede usarse en EF, otro o, o directamente mediante una aplicación, para obtener instancias de conexiones, comandos, parámetros y otras abstracciones de ADO.NET de un modo independiente del proveedor. Puede encontrar más información sobre DbProviderFactory en la [documentación de MSDN para ADO.net](https://msdn.microsoft.com/library/a6cd7c08.aspx).

### <a name="dbproviderservices"></a>DbProviderServices

EF depende de tener un tipo derivado de DbProviderServices para proporcionar funcionalidad adicional que EF necesita en la parte superior de la funcionalidad que ya proporciona el proveedor ADO.NET. En versiones anteriores de EF la clase DbProviderServices formaba parte del .NET Framework y se encontraba en el espacio de nombres System. Data. Common. A partir de EF6, esta clase ahora forma parte de EntityFramework.dll y está en el espacio de nombres System. Data. Entity. Core. Common.

Puede encontrar más información sobre la funcionalidad fundamental de una implementación de DbProviderServices en [MSDN](https://msdn.microsoft.com/library/ee789835.aspx). Sin embargo, tenga en cuenta que, en el momento de escribir esta información, no se actualiza para EF6, aunque la mayoría de los conceptos siguen siendo válidos. Las implementaciones de SQL Server y SQL Server Compact de DbProviderServices también se protegen en el [código base de código abierto](https://github.com/aspnet/EntityFramework6/) y pueden servir como referencias útiles para otras implementaciones.

En versiones anteriores de EF, la implementación de DbProviderServices que se va a usar se obtuvo directamente de un proveedor ADO.NET. Esto se realiza mediante la conversión de DbProviderFactory a IServiceProvider y la llamada al método GetService. Este es el proveedor de EF estrechamente acoplado al DbProviderFactory. Este acoplamiento ha bloqueado EF para que no se mueva fuera del .NET Framework y, por tanto, para EF6 se ha quitado este acoplamiento estricto y ahora se ha registrado una implementación de DbProviderServices directamente en el archivo de configuración de la aplicación o en la configuración basada en código, tal y como se describe más detalladamente en la sección _registrar DbProviderServices_ que se muestra a continuación.

## <a name="additional-services"></a>Servicios adicionales

Además de los servicios fundamentales descritos anteriormente, también hay muchos otros servicios que se usan en EF, que siempre son específicos de cada proveedor. Las implementaciones predeterminadas específicas del proveedor de estos servicios se pueden proporcionar mediante una implementación de DbProviderServices. Las aplicaciones también pueden invalidar las implementaciones de estos servicios o proporcionar implementaciones cuando un tipo DbProviderServices no proporciona un valor predeterminado. Esto se describe con más detalle en la sección _resolución de servicios adicionales_ más adelante.

A continuación se enumeran los tipos de servicio adicionales que un proveedor puede ser de interés para un proveedor. Encontrará más detalles sobre cada uno de estos tipos de servicio en la documentación de la API.

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

Se trata de un servicio opcional que permite a un proveedor implementar reintentos u otro comportamiento cuando las consultas y los comandos se ejecutan en la base de datos. Si no se proporciona ninguna implementación, EF simplemente ejecutará los comandos y propagará las excepciones que se produzcan. Por SQL Server este servicio se utiliza para proporcionar una directiva de reintentos que es especialmente útil cuando se ejecuta en servidores de bases de datos basados en la nube, como SQL Azure.

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

Se trata de un servicio opcional que permite a un proveedor crear objetos DbConnection por Convención cuando se especifica solo un nombre de base de datos. Tenga en cuenta que aunque este servicio se puede resolver mediante una implementación de DbProviderServices, está presente desde EF 4,1 y también se puede establecer explícitamente en el archivo de configuración o en el código. El proveedor solo tendrá la oportunidad de resolver este servicio si se registró como el proveedor predeterminado (vea _el proveedor predeterminado_ a continuación) y si no se ha establecido un generador de conexiones predeterminado en otro lugar.

### <a name="dbspatialservices"></a>DbSpatialServices

Se trata de un servicio opcional que permite a un proveedor agregar compatibilidad para los tipos espaciales Geography y Geometry. Se debe proporcionar una implementación de este servicio para que una aplicación use EF con tipos espaciales. DbSptialServices se solicita de dos maneras. En primer lugar, se solicitan servicios espaciales específicos del proveedor mediante un objeto DbProviderInfo (que contiene el nombre invariable y el token del manifiesto) como clave. En segundo lugar, se puede solicitar a DbSpatialServices sin clave. Se usa para resolver el "proveedor espacial global" que se usa al crear tipos independientes de DbGeography o DbGeometry.

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

Se trata de un servicio opcional que permite usar migraciones de EF para la generación de SQL que se usa en la creación y modificación de esquemas de base de datos de Code First. Se requiere una implementación para admitir las migraciones. Si se proporciona una implementación, también se utilizará cuando se creen las bases de datos mediante inicializadores de base de datos o el método Database. Create.

### <a name="funcdbconnection-string-historycontextfactory"></a>FUNC<DbConnection, String, HistoryContextFactory>

Se trata de un servicio opcional que permite a un proveedor configurar la asignación de HistoryContext a la `__MigrationHistory` tabla usada por las migraciones de EF. HistoryContext es un DbContext de Code First y se puede configurar mediante la API fluida normal para cambiar aspectos como el nombre de la tabla y las especificaciones de asignación de columnas. La implementación predeterminada de este servicio devuelta por EF para todos los proveedores puede funcionar para un servidor de base de datos determinado si ese proveedor admite todas las asignaciones predeterminadas de tabla y columna. En tal caso, no es necesario que el proveedor proporcione una implementación de este servicio.

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

Se trata de un servicio opcional para obtener el DbProviderFactory correcto de un objeto DbConnection determinado. La implementación predeterminada de este servicio devuelta por EF para todos los proveedores está pensada para funcionar con todos los proveedores. Sin embargo, cuando se ejecuta en .NET 4, el DbProviderFactory no es accesible públicamente desde uno si su DbConnections. Por lo tanto, EF usa algunas heurísticas para buscar coincidencias en los proveedores registrados. Es posible que, en algunos proveedores, se produzca un error en esta heurística y, en tales situaciones, el proveedor proporcione una nueva implementación.

## <a name="registering-dbproviderservices"></a>Registrando DbProviderServices

La implementación de DbProviderServices que se va a usar se puede registrar en el archivo de configuración de la aplicación (app.config o web.config) o mediante la configuración basada en código. En cualquier caso, el registro utiliza el "nombre invariable" del proveedor como clave. Esto permite registrar y usar varios proveedores en una sola aplicación. El nombre invariable que se usa para los registros EF es el mismo que el nombre invariable que se usa para el registro del proveedor ADO.NET y las cadenas de conexión. Por ejemplo, para SQL Server se usa el nombre invariable "System. Data. SqlClient".

### <a name="config-file-registration"></a>Registro en el archivo de configuración

El tipo DbProviderServices que se va a usar se registra como un elemento de proveedor en la lista de proveedores de la sección entityFramework del archivo de configuración de la aplicación. Por ejemplo:

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

La cadena de _tipo_ debe ser el nombre de tipo calificado con el ensamblado de la implementación de DbProviderServices que se va a usar.

### <a name="code-based-registration"></a>Registro basado en código

A partir de, los proveedores de EF6 también se pueden registrar mediante código. Esto permite usar un proveedor EF sin ningún cambio en el archivo de configuración de la aplicación. Para usar la configuración basada en código, una aplicación debe crear una clase DbConfiguration tal como se describe en la [documentación de configuración basada en código](https://msdn.com/data/jj680699). El constructor de la clase DbConfiguration debería llamar a SetProviderServices para registrar el proveedor de EF. Por ejemplo:

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>Resolver servicios adicionales

Como se mencionó anteriormente en la sección _información general_ de los tipos de proveedor, una clase DbProviderServices también se puede usar para resolver servicios adicionales. Esto es posible porque DbProviderServices implementa IDbDependencyResolver y cada tipo de DbProviderServices registrado se agrega como una "resolución predeterminada". El mecanismo IDbDpendencyResolver se describe con más detalle en [resolución de dependencias](xref:ef6/fundamentals/configuring/dependency-resolution). Sin embargo, no es necesario comprender todos los conceptos de esta especificación para resolver servicios adicionales en un proveedor.

La forma más común de que un proveedor resuelva servicios adicionales es llamar a DbProviderServices. AddDependencyResolver para cada servicio en el constructor de la clase DbProviderServices. Por ejemplo, SqlProviderServices (el proveedor de EF para SQL Server) tiene un código similar a este para la inicialización:

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

Este constructor usa las siguientes clases auxiliares:

*   SingletonDependencyResolver: proporciona una manera sencilla de resolver los servicios singleton, es decir, los servicios para los que se devuelve la misma instancia cada vez que se llama a GetService. Los servicios transitorios suelen registrarse como un generador singleton que se usará para crear instancias transitorias a petición.
*   ExecutionStrategyResolver: un solucionador específico para devolver implementaciones de IExecutionStrategy.

En lugar de usar DbProviderServices. AddDependencyResolver, también es posible invalidar DbProviderServices. GetService y resolver servicios adicionales directamente. Se llamará a este método cuando EF necesite un servicio definido por un tipo determinado y, en algunos casos, para una clave determinada. El método debe devolver el servicio si es posible o devolver null a la cancelación de la devolución del servicio y, en su lugar, permitir que otra clase la resuelva. Por ejemplo, para resolver el generador de conexiones predeterminado, el código de GetService podría tener un aspecto similar al siguiente:

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>Orden de registro

Cuando se registran varias implementaciones de DbProviderServices en el archivo de configuración de una aplicación, se agregarán como resoluciones secundarias en el orden en que se muestran. Puesto que los solucionadores siempre se agregan a la parte superior de la cadena de resolución secundaria, esto significa que el proveedor al final de la lista tendrá la oportunidad de resolver las dependencias antes de las demás. (Esto puede parecer un poco más intuitivo al principio, pero tiene sentido si se deja que cada proveedor esté fuera de la lista y se apile encima de los proveedores existentes).

Normalmente, esta clasificación no es importante porque la mayoría de los servicios del proveedor son específicos del proveedor y tienen como clave el nombre invariable del proveedor. Sin embargo, para los servicios que no están codificados por el nombre invariable del proveedor o alguna otra clave específica del proveedor, el servicio se resolverá en función de este orden. Por ejemplo, si no se establece explícitamente de forma distinta en otro lugar, el generador de conexiones predeterminado procederá del proveedor superior de la cadena.

## <a name="additional-config-file-registrations"></a>Registros de archivos de configuración adicionales

Es posible registrar explícitamente algunos de los servicios de proveedor adicionales descritos anteriormente en el archivo de configuración de una aplicación. Cuando esto se hace, se usará el registro del archivo de configuración en lugar de cualquier elemento devuelto por el método GetService de la implementación de DbProviderServices.

### <a name="registering-the-default-connection-factory"></a>Registro del generador de conexiones predeterminado

A partir de EF5, el paquete NuGet de EntityFramework registra automáticamente el generador de conexiones de SQL Express o el generador de conexiones de LocalDb en el archivo de configuración.

Por ejemplo:

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

El _tipo_ es el nombre de tipo calificado con el ensamblado para el generador de conexiones predeterminado, que debe implementar IDbConnectionFactory.

Se recomienda que un paquete NuGet de proveedor establezca el generador de conexiones predeterminado de este modo cuando se instale. Consulte los _paquetes de NuGet para los proveedores_ siguientes.

## <a name="additional-ef6-provider-changes"></a>Cambios adicionales del proveedor de EF6

### <a name="spatial-provider-changes"></a>Cambios del proveedor espacial

Los proveedores que admiten tipos espaciales ahora deben implementar algunos métodos adicionales en las clases que derivan de DbSpatialDataReader:

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

También hay nuevas versiones asincrónicas de los métodos existentes que se recomienda invalidar como las implementaciones predeterminadas que se delegan en los métodos sincrónicos y, por tanto, no se ejecutan de forma asincrónica:

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>Compatibilidad nativa con Enumerable. Contains

EF6 introduce un nuevo tipo de expresión, DbInExpression, que se ha agregado para solucionar problemas de rendimiento relacionados con el uso de Enumerable. Contains en consultas LINQ. La clase DbProviderManifest tiene un nuevo método virtual, SupportsInExpression, al que se llama EF para determinar si un proveedor controla el nuevo tipo de expresión. Para ofrecer compatibilidad con las implementaciones de proveedor existentes, el método devuelve false. Para beneficiarse de esta mejora, un proveedor de EF6 puede agregar código para controlar DbInExpression e invalidar SupportsInExpression para que devuelva true. Se puede crear una instancia de DbInExpression llamando al método DbExpressionBuilder.In. Una instancia de DbInExpression se compone de un DbExpression, que normalmente representa una columna de tabla, y una lista de DbConstantExpression para comprobar si hay coincidencias.

## <a name="nuget-packages-for-providers"></a>Paquetes NuGet para proveedores

Una manera de hacer que un proveedor de EF6 esté disponible es publicarlo como un paquete de NuGet. El uso de un paquete NuGet presenta las siguientes ventajas:

*   Es fácil usar NuGet para agregar el registro del proveedor al archivo de configuración de la aplicación.
*   Se pueden realizar cambios adicionales en el archivo de configuración para establecer el generador de conexiones predeterminado de modo que las conexiones realizadas por la Convención utilicen el proveedor registrado
*   NuGet controla la adición de redirecciones de enlace para que el proveedor de EF6 siga funcionando incluso después de que se publique un nuevo paquete EF.

Un ejemplo de esto es el paquete EntityFramework. Sqlservercom que se incluye en el código [base de código abierto](https://github.com/aspnet/entityframework6). Este paquete proporciona una buena plantilla para crear paquetes NuGet del proveedor de EF.

### <a name="powershell-commands"></a>Comandos de PowerShell

Cuando se instala el paquete NuGet EntityFramework, se registra un módulo de PowerShell que contiene dos comandos que son muy útiles para los paquetes de proveedor:

*   Add-EFProvider agrega una nueva entidad para el proveedor en el archivo de configuración del proyecto de destino y se asegura de que está al final de la lista de proveedores registrados.
*   Add-EFDefaultConnectionFactory agrega o actualiza el registro de defaultConnectionFactory en el archivo de configuración del proyecto de destino.

Ambos comandos se encargan de agregar una sección entityFramework al archivo de configuración y agregar una colección de proveedores si es necesario.

Está previsto que se llame a estos comandos desde el install.ps1 script de NuGet. Por ejemplo, install.ps1 para el proveedor de SQL Compact tiene un aspecto similar al siguiente:

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

Puede obtener más información acerca de estos comandos mediante Get-Help en la ventana de la consola del administrador de paquetes.

## <a name="wrapping-providers"></a>Encapsular proveedores

Un proveedor de ajuste es un proveedor EF o ADO.NET que contiene un proveedor existente para extenderlo con otras funciones, como la generación de perfiles o las capacidades de seguimiento. Los proveedores de encapsulado se pueden registrar de la manera normal, pero a menudo es más conveniente configurar el proveedor de ajuste en tiempo de ejecución mediante la interceptación de la resolución de servicios relacionados con el proveedor. Para ello, se puede usar el evento estático OnLockingConfiguration en la clase DbConfiguration.

Se llama a OnLockingConfiguration después de que EF haya determinado dónde se obtendrá la configuración de EF para el dominio de aplicación, pero antes de que se bloquee para su uso. En el inicio de la aplicación (antes de que se use EF), la aplicación debe registrar un controlador de eventos para este evento. (Se está considerando la posibilidad de agregar compatibilidad para registrar este controlador en el archivo de configuración, pero aún no se admite). A continuación, el controlador de eventos debe realizar una llamada a ReplaceService para cada servicio que debe ajustarse.  

Por ejemplo, para ajustar IDbConnectionFactory y DbProviderService, se debe registrar un controlador similar a este:

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

El servicio que se ha resuelto y ahora debe ajustarse junto con la clave que se usó para resolver el servicio se pasa al controlador. Después, el controlador puede encapsular este servicio y reemplazar el servicio devuelto por la versión ajustada.

## <a name="resolving-a-dbproviderfactory-with-ef"></a>Resolver un DbProviderFactory con EF

DbProviderFactory es uno de los tipos fundamentales de proveedor que requiere EF como se describe en la sección de _información general de tipos de proveedor_ anterior. Como ya se mencionó, no es un tipo de EF y el registro no suele ser parte de la configuración de EF, sino que es el registro de proveedor ADO.NET normal en el archivo de configuración de la aplicación o el archivo de machine.config.

A pesar de que este EF siga usando su mecanismo de resolución de dependencias normal al buscar un DbProviderFactory para usarlo. El solucionador predeterminado usa el registro ADO.NET normal en los archivos de configuración y, por lo tanto, esto suele ser transparente. Sin embargo, debido al mecanismo de resolución de dependencias normal se usa, significa que se puede usar una IDbDependencyResolver para resolver un DbProviderFactory incluso cuando no se ha realizado el registro de ADO.NET normal.

La resolución de DbProviderFactory de esta manera tiene varias implicaciones:

*   Una aplicación que usa la configuración basada en código puede agregar llamadas en su clase DbConfiguration para registrar el DbProviderFactory adecuado. Esto es especialmente útil para las aplicaciones que no quieren (o no pueden) hacer uso de cualquier configuración basada en archivos.
*   El servicio se puede ajustar o reemplazar mediante ReplaceService tal como se describe en la sección anterior de los _proveedores de ajuste_
*   Teóricamente, una implementación de DbProviderServices podría resolver un DbProviderFactory.

El punto importante que hay que tener en cuenta es que solo afectará a la búsqueda de DbProviderFactory en EF. Otro código que no sea EF podría esperar que el proveedor ADO.NET se registre de la forma normal y puede producir un error si no se encuentra el registro. Por esta razón, suele ser mejor registrar un DbProviderFactory de la manera normal de ADO.NET.

### <a name="related-services"></a>Servicios relacionados

Si EF se usa para resolver un DbProviderFactory, debe resolver también los servicios IProviderInvariantName y IDbProviderFactoryResolver.

IProviderInvariantName es un servicio que se usa para determinar un nombre invariable de proveedor para un tipo determinado de DbProviderFactory. La implementación predeterminada de este servicio utiliza el registro del proveedor ADO.NET. Esto significa que si el proveedor ADO.NET no está registrado de la manera normal porque EF está resolviendo DbProviderFactory, también será necesario para resolver este servicio. Tenga en cuenta que se agrega automáticamente un solucionador para este servicio cuando se usa el método DbConfiguration. SetProviderFactory.

Tal y como se describe en la sección de _información general sobre tipos de proveedores_ anterior, IDbProviderFactoryResolver se usa para obtener el DbProviderFactory correcto de un objeto DbConnection determinado. La implementación predeterminada de este servicio cuando se ejecuta en .NET 4 usa el registro del proveedor ADO.NET. Esto significa que si el proveedor ADO.NET no está registrado de la manera normal porque EF está resolviendo DbProviderFactory, también será necesario para resolver este servicio.
