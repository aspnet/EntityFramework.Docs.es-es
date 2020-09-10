---
title: 'Resolución de dependencias: EF6'
description: Resolución de dependencias en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 32d19ac6-9186-4ae1-8655-64ee49da55d0
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: c23253dc5a413077e3980fcfa18ea83b5fc3970e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618417"
---
# <a name="dependency-resolution"></a>Resolución de dependencias
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

A partir de EF6, Entity Framework contiene un mecanismo de uso general para obtener implementaciones de servicios que requiere. Es decir, cuando EF usa una instancia de algunas interfaces o clases base, solicitará una implementación concreta de la interfaz o la clase base que se va a usar. Esto se logra mediante el uso de la interfaz IDbDependencyResolver:  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

EF normalmente llama al método GetService y se controla mediante una implementación de IDbDependencyResolver proporcionada por EF o por la aplicación. Cuando se llama, el argumento de tipo es la interfaz o el tipo de clase base del servicio que se solicita, y el objeto de clave es null o un objeto que proporciona información contextual sobre el servicio solicitado.  

A menos que se indique lo contrario, cualquier objeto devuelto debe ser seguro para subprocesos, ya que se puede utilizar como singleton. En muchos casos, el objeto devuelto es un generador, en cuyo caso el propio generador debe ser seguro para subprocesos, pero no es necesario que el objeto devuelto por el generador sea seguro para subprocesos, ya que se solicita una nueva instancia desde el generador para cada uso.

En este artículo no se incluyen detalles completos sobre cómo implementar IDbDependencyResolver, sino que actúa como referencia para los tipos de servicio (es decir, la interfaz y los tipos de clase base) para los que EF llama a GetService y la semántica del objeto de clave para cada una de estas llamadas.

## <a name="systemdataentityidatabaseinitializertcontext"></a>System. Data. Entity. IDatabaseInitializer<TContext\>  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: inicializador de base de datos para el tipo de contexto especificado  

**Clave**: no se utiliza; será null  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>FUNC<System. Data. Entity. Migrations. SQL. MigrationSqlGenerator\>  

**Versión introducida**: EF 6.0.0

**Objeto devuelto**: generador para crear un generador de SQL que se puede usar para las migraciones y otras acciones que hacen que se cree una base de datos, como la creación de bases de datos con inicializadores de base de datos.  

**Key**: una cadena que contiene el nombre invariable del proveedor ADO.net que especifica el tipo de base de datos para el que se generará SQL. Por ejemplo, se devuelve el generador de SQL Server SQL para la clave "System. Data. SqlClient".  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycorecommondbproviderservices"></a>System. Data. Entity. Core. Common. DbProviderServices  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: el proveedor EF que se va a usar para un nombre invariable de proveedor determinado  

**Key**: una cadena que contiene el nombre invariable del proveedor ADO.net que especifica el tipo de base de datos para el que se necesita un proveedor. Por ejemplo, se devuelve el proveedor de SQL Server para la clave "System. Data. SqlClient".  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System. Data. Entity. Infrastructure. IDbConnectionFactory  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: el generador de conexión que se usará cuando EF cree una conexión de base de datos por Convención. Es decir, cuando no se proporciona ninguna conexión o cadena de conexión a EF, y no se encuentra ninguna cadena de conexión en el app.config o web.config, este servicio se usa para crear una conexión por Convención. Cambiar el generador de conexiones puede permitir que EF use otro tipo de base de datos (por ejemplo, SQL Server Compact Edition) de forma predeterminada.  

**Clave**: no se utiliza; será null  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System. Data. Entity. Infrastructure. IManifestTokenService  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un servicio que puede generar un token del manifiesto del proveedor a partir de una conexión. Este servicio se usa normalmente de dos maneras. En primer lugar, se puede usar para evitar Code First conectarse a la base de datos al compilar un modelo. En segundo lugar, se puede usar para forzar Code First para generar un modelo para una versión de base de datos específica, por ejemplo, para forzar un modelo para SQL Server 2005 incluso si se usa a veces SQL Server 2008.  

**Duración del objeto**: singleton: el mismo objeto se puede usar varias veces y simultáneamente en diferentes subprocesos  

**Clave**: no se utiliza; será null  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System. Data. Entity. Infrastructure. IDbProviderFactoryService  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un servicio que puede obtener un generador de proveedores de una conexión determinada. En .NET 4,5, el proveedor es accesible públicamente desde la conexión. En .NET 4, la implementación predeterminada de este servicio usa algunas heurísticas para encontrar el proveedor coincidente. Si se produce un error, se puede registrar una nueva implementación de este servicio para proporcionar una solución adecuada.  

**Clave**: no se utiliza; será null  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>FUNC<DbContext, System. Data. Entity. Infrastructure. IDbModelCacheKey\>  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un generador que generará una clave de caché del modelo para un contexto determinado. De forma predeterminada, EF almacena en caché un modelo por tipo DbContext por proveedor. Se puede usar una implementación diferente de este servicio para agregar otra información, como el nombre de esquema, a la clave de caché.  

**Clave**: no se utiliza; será null  

## <a name="systemdataentityspatialdbspatialservices"></a>System. Data. Entity. Spatial. DbSpatialServices  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un proveedor espacial de EF que agrega compatibilidad con el proveedor de EF básico para los tipos espaciales Geography y Geometry.  

**Clave**: DbSptialServices se solicita de dos maneras. En primer lugar, se solicitan servicios espaciales específicos del proveedor mediante un objeto DbProviderInfo (que contiene el nombre invariable y el token del manifiesto) como clave. En segundo lugar, se puede solicitar a DbSpatialServices sin clave. Se usa para resolver el "proveedor espacial global", que se usa al crear tipos independientes de DbGeography o DbGeometry.  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>FUNC<System. Data. Entity. Infrastructure. IDbExecutionStrategy\>  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: generador para crear un servicio que permite a un proveedor implementar reintentos u otro comportamiento cuando las consultas y los comandos se ejecutan en la base de datos. Si no se proporciona ninguna implementación, EF simplemente ejecutará los comandos y propagará las excepciones que se produzcan. Por SQL Server este servicio se utiliza para proporcionar una directiva de reintentos que es especialmente útil cuando se ejecuta en servidores de bases de datos basados en la nube, como SQL Azure.  

**Key**: un objeto ExecutionStrategyKey que contiene el nombre invariable del proveedor y, opcionalmente, un nombre de servidor para el que se usará la estrategia de ejecución.  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>FUNC<DbConnection, String, System. Data. Entity. Migrations. History. HistoryContext\>  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un generador que permite a un proveedor configurar la asignación de HistoryContext a la `__MigrationHistory` tabla usada por las migraciones de EF. HistoryContext es un DbContext de Code First y se puede configurar mediante la API fluida normal para cambiar aspectos como el nombre de la tabla y las especificaciones de asignación de columnas.  

**Clave**: no se utiliza; será null  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdatacommondbproviderfactory"></a>System. Data. Common. DbProviderFactory  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: el proveedor ADO.net que se va a usar para un nombre invariable de proveedor determinado.  

**Key**: una cadena que contiene el nombre invariable del proveedor ADO.net  

>[!NOTE]
> Normalmente, este servicio no se cambia directamente, ya que la implementación predeterminada usa el registro del proveedor ADO.NET normal. Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System. Data. Entity. Infrastructure. IProviderInvariantName  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un servicio que se utiliza para determinar un nombre invariable de proveedor para un tipo determinado de DbProviderFactory. La implementación predeterminada de este servicio utiliza el registro del proveedor ADO.NET. Esto significa que si el proveedor ADO.NET no está registrado de la manera normal porque EF está resolviendo DbProviderFactory, también será necesario para resolver este servicio.  

**Key**: la instancia de DbProviderFactory para la que se requiere un nombre invariable.  

>[!NOTE]
> Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System. Data. Entity. Core. Mapping. ViewGeneration. IViewAssemblyCache  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: memoria caché de los ensamblados que contienen las vistas generadas previamente. Un reemplazo se usa normalmente para permitir que EF sepa qué ensamblados contienen vistas generadas previamente sin realizar ninguna detección.  

**Clave**: no se utiliza; será null  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System. Data. Entity. Infrastructure. pluralización. IPluralizationService

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un servicio utilizado por EF para pluralar y singularar los nombres. De forma predeterminada, se usa un servicio de pluralización en inglés.  

**Clave**: no se utiliza; será null  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System. Data. Entity. Infrastructure. intercepción. IDbInterceptor  

**Versión introducida**: EF 6.0.0

**Objetos devueltos**: todos los interceptores que se deben registrar cuando se inicia la aplicación. Tenga en cuenta que estos objetos se solicitan mediante la llamada a GetServices y todos los interceptores devueltos por cualquier solucionador de dependencias se registrarán.

**Clave**: no se utiliza; será null.  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>FUNC<System. Data. Entity. DbContext, Action<String \> , System. Data. Entity. Infrastructure. intercepción. DatabaseLogFormatter\>  

**Versión introducida**: EF 6.0.0  

**Objeto devuelto**: un generador que se usará para crear el formateador del registro de base de datos que se utilizará cuando el contexto. La propiedad Database. log está establecida en el contexto especificado.  

**Clave**: no se utiliza; será null.  

## <a name="funcsystemdataentitydbcontext"></a>FUNC<System. Data. Entity. DbContext\>  

**Versión introducida**: EF 6.1.0  

**Objeto devuelto**: un generador que se usará para crear instancias de contexto para las migraciones cuando el contexto no tenga un constructor sin parámetros accesible.  

**Key**: el objeto de tipo para el tipo de DbContext derivado para el que se necesita un generador.  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>FUNC<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\>  

**Versión introducida**: EF 6.1.0  

**Objeto devuelto**: un generador que se utilizará para crear serializadores para la serialización de anotaciones personalizadas fuertemente tipadas de modo que se puedan serializar y desesterilizar en XML para su uso en migraciones de Code First.  

**Key**: nombre de la anotación que se va a serializar o deserializar.  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>FUNC<System. Data. Entity. Infrastructure. TransactionHandler\>  

**Versión introducida**: EF 6.1.0  

**Objeto devuelto**: un generador que se usará para crear controladores para las transacciones, de modo que se pueda aplicar un control especial para situaciones como el control de errores de confirmación.  

**Key**: un objeto ExecutionStrategyKey que contiene el nombre invariable del proveedor y, opcionalmente, un nombre de servidor para el que se usará el controlador de transacciones.  
