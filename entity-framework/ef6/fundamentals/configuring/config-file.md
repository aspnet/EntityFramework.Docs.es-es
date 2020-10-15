---
title: 'Configuración del archivo de configuración: EF6'
description: Configuración del archivo de configuración en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: fc991810e93840c27e6631dfb5bc1796c1328d37
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063327"
---
# <a name="configuration-file-settings"></a>Configuración del archivo de configuración
Entity Framework permite especificar una serie de valores desde el archivo de configuración. En general, EF sigue un comportamiento de "Convención sobre configuración": todos los valores de configuración descritos en esta publicación tienen un comportamiento predeterminado, solo tiene que preocuparse por cambiar el valor cuando el valor predeterminado ya no satisface sus requisitos.  

## <a name="a-code-based-alternative"></a>Code-Based alternativa  

Todos estos valores también se pueden aplicar mediante código. A partir de EF6, se presentó la [configuración basada en código](xref:ef6/fundamentals/configuring/code-based), que proporciona una manera centralizada de aplicar la configuración desde el código. Antes de EF6, todavía se puede aplicar la configuración desde el código, pero es necesario usar varias API para configurar distintas áreas. La opción del archivo de configuración permite cambiar fácilmente esta configuración durante la implementación sin necesidad de actualizar el código.

## <a name="the-entity-framework-configuration-section"></a>La sección de configuración Entity Framework  

A partir de EF 4.1, podría establecer el inicializador de base de datos para un contexto mediante la sección **appSettings** del archivo de configuración. En EF 4,3, se presentó la sección **entityFramework** personalizada para controlar la nueva configuración. Entity Framework seguirá reconociendo los inicializadores de base de datos establecidos con el formato antiguo, pero se recomienda pasar al nuevo formato siempre que sea posible.

La sección **entityFramework** se agregó automáticamente al archivo de configuración del proyecto al instalar el paquete NuGet entityFramework.  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a>Cadenas de conexión  

En [esta página](xref:ef6/fundamentals/configuring/connection-strings) se proporcionan más detalles sobre cómo Entity Framework determina la base de datos que se va a usar, incluidas las cadenas de conexión en el archivo de configuración.  

Las cadenas de conexión van en el elemento **connectionStrings** estándar y no requieren la sección **entityFramework** .  

Los modelos basados en Code First usan cadenas de conexión ADO.NET normales. Por ejemplo:  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

Los modelos basados en el diseñador EF usan cadenas de conexión EF especiales. Por ejemplo:  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a>Code-Based tipo de configuración (EF6 en adelante)  

A partir de EF6, puede especificar el DbConfiguration para EF que se va a usar para la [configuración basada en código](xref:ef6/fundamentals/configuring/code-based) en la aplicación. En la mayoría de los casos, no es necesario especificar esta configuración, ya que EF detectará automáticamente el DbConfiguration. Para obtener detalles sobre Cuándo es posible que tenga que especificar DbConfiguration en el archivo de configuración, consulte la sección **mover DbConfiguration** de [configuración basada en código](xref:ef6/fundamentals/configuring/code-based).  

Para establecer un tipo DbConfiguration, especifique el nombre del tipo calificado con el ensamblado en el elemento **codeConfigurationType** .  

> [!NOTE]
> Un nombre completo de ensamblado es el nombre completo del espacio de nombres, seguido de una coma, el ensamblado en el que reside el tipo. También puede especificar la versión del ensamblado, la referencia cultural y el token de clave pública.  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>Proveedores de bases de datos de EF (EF6 en adelante)  

Antes de EF6, los elementos específicos de Entity Framework de un proveedor de base de datos debían incluirse como parte del proveedor ADO.NET básico. A partir de EF6, las partes específicas de EF ahora se administran y registran por separado.  

Normalmente no necesitará registrar los proveedores. Esto lo realiza normalmente el proveedor cuando se instala.  

Los proveedores se registran mediante la inclusión de un elemento de **proveedor** en la sección de **proveedores** secundarios de la sección **entityFramework** . Hay dos atributos necesarios para una entrada de proveedor:  

- **invariantName** identifica el proveedor ADO.net principal al que se destina este proveedor EF  
- **Type** es el nombre de tipo calificado con el ensamblado de la implementación del proveedor EF  

> [!NOTE]
> Un nombre completo de ensamblado es el nombre completo del espacio de nombres, seguido de una coma, el ensamblado en el que reside el tipo. También puede especificar la versión del ensamblado, la referencia cultural y el token de clave pública.  

Como ejemplo, aquí se muestra la entrada que se crea para registrar el proveedor de SQL Server predeterminado al instalar Entity Framework.  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>Interceptores (EF 6.1 y versiones posteriores)  

A partir de EF 6.1, puede registrar los interceptores en el archivo de configuración. Los interceptores permiten ejecutar lógica adicional cuando EF realiza ciertas operaciones, como la ejecución de consultas de base de datos, la apertura de conexiones, etc.  

Los interceptores se registran mediante la inclusión de un elemento **interceptor** en la sección de **interceptores** secundaria de la sección **entityFramework** . Por ejemplo, la configuración siguiente registra el interceptor de **DatabaseLogger** integrado que registrará todas las operaciones de base de datos en la consola.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>Registro de operaciones de base de datos en un archivo (EF 6.1 en adelante)  

El registro de los interceptores mediante el archivo de configuración es especialmente útil si desea agregar el registro a una aplicación existente para ayudar a depurar un problema. **DatabaseLogger** admite el registro en un archivo proporcionando el nombre de archivo como un parámetro de constructor.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

De forma predeterminada, esto hará que el archivo de registro se sobrescriba con un nuevo archivo cada vez que se inicie la aplicación. En su lugar, anexe al archivo de registro si ya existe, use algo parecido a:  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

Para obtener información adicional sobre **DatabaseLogger** y el registro de interceptores, consulte la entrada de blog [EF 6,1: activar el registro sin volver a compilar](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).  

## <a name="code-first-default-connection-factory"></a>Code First factoría de conexión predeterminada  

La sección configuración permite especificar un generador de conexiones predeterminado que Code First debe usar para buscar una base de datos que se utilizará para un contexto. El generador de conexiones predeterminado solo se usa cuando no se ha agregado ninguna cadena de conexión al archivo de configuración para un contexto.  

Al instalar el paquete de NuGet de EF, se registró una factoría de conexión predeterminada que señala a SQL Express o a LocalDB, en función de la que haya instalado.  

Para establecer un generador de conexiones, especifique el nombre del tipo calificado con el ensamblado en el elemento **defaultConnectionFactory** .  

> [!NOTE]
> Un nombre completo de ensamblado es el nombre completo del espacio de nombres, seguido de una coma, el ensamblado en el que reside el tipo. También puede especificar la versión del ensamblado, la referencia cultural y el token de clave pública.  

A continuación se muestra un ejemplo de configuración de su propio generador de conexiones predeterminado:  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

En el ejemplo anterior se requiere que el generador personalizado tenga un constructor sin parámetros. Si es necesario, puede especificar parámetros de constructor mediante el elemento **Parameters** .  

Por ejemplo, SqlCeConnectionFactory, que se incluye en Entity Framework, requiere que proporcione un nombre invariable de proveedor al constructor. El nombre invariable del proveedor identifica la versión de SQL Compact que quiere usar. La configuración siguiente hará que los contextos usen SQL Compact versión 4,0 de forma predeterminada.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Si no establece un generador de conexiones predeterminado, Code First usa SqlConnectionFactory, que apunta a `.\SQLEXPRESS` . SqlConnectionFactory también tiene un constructor que permite invalidar partes de la cadena de conexión. Si desea utilizar una instancia de SQL Server distinta de `.\SQLEXPRESS` , puede usar este constructor para establecer el servidor.  

La configuración siguiente hará que Code First use **MyDatabaseServer** para los contextos que no tienen un conjunto de cadenas de conexión explícitas.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

De forma predeterminada, se supone que los argumentos del constructor son del tipo cadena. Puede usar el atributo type para cambiar esto.  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>Inicializadores de base de datos  

Los inicializadores de base de datos se configuran en función del contexto. Se pueden establecer en el archivo de configuración mediante el elemento de **contexto** . Este elemento usa el nombre completo del ensamblado para identificar el contexto que se está configurando.  

De forma predeterminada, Code First contextos se configuran para usar el inicializador CreateDatabaseIfNotExists. Hay un atributo **disableDatabaseInitialization** en el elemento de **contexto** que se puede usar para deshabilitar la inicialización de la base de datos.  

Por ejemplo, la siguiente configuración deshabilita la inicialización de la base de datos para el contexto blog. BlogContext definido en MyAssembly.dll.  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

Puede usar el elemento **databaseInitializer** para establecer un inicializador personalizado.  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

Los parámetros de constructor utilizan la misma sintaxis que los generadores de conexiones predeterminados.  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

Puede configurar uno de los inicializadores de base de datos genéricos que se incluyen en Entity Framework. El atributo **Type** utiliza el formato de .NET Framework para los tipos genéricos.  

Por ejemplo, si usa Migraciones de Code First, puede configurar la base de datos que se va a migrar automáticamente mediante el `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` inicializador.  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
