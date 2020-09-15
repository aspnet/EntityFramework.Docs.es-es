---
title: 'Configuración del archivo de configuración: EF6'
description: Configuración del archivo de configuración en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: b407ae7d64df18f6a8d80aa13703a8c243c6f3a2
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070723"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="91c78-103">Configuración del archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="91c78-103">Configuration File Settings</span></span>
<span data-ttu-id="91c78-104">Entity Framework permite especificar una serie de valores desde el archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="91c78-104">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="91c78-105">En general, EF sigue un comportamiento de "Convención sobre configuración": todos los valores de configuración descritos en esta publicación tienen un comportamiento predeterminado, solo tiene que preocuparse por cambiar el valor cuando el valor predeterminado ya no satisface sus requisitos.</span><span class="sxs-lookup"><span data-stu-id="91c78-105">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="91c78-106">Una alternativa basada en código</span><span class="sxs-lookup"><span data-stu-id="91c78-106">A Code-Based Alternative</span></span>  

<span data-ttu-id="91c78-107">Todos estos valores también se pueden aplicar mediante código.</span><span class="sxs-lookup"><span data-stu-id="91c78-107">All of these settings can also be applied using code.</span></span> <span data-ttu-id="91c78-108">A partir de EF6, se presentó la [configuración basada en código](xref:ef6/fundamentals/configuring/code-based), que proporciona una manera centralizada de aplicar la configuración desde el código.</span><span class="sxs-lookup"><span data-stu-id="91c78-108">Starting in EF6 we introduced [code-based configuration](xref:ef6/fundamentals/configuring/code-based), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="91c78-109">Antes de EF6, todavía se puede aplicar la configuración desde el código, pero es necesario usar varias API para configurar distintas áreas.</span><span class="sxs-lookup"><span data-stu-id="91c78-109">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="91c78-110">La opción del archivo de configuración permite cambiar fácilmente esta configuración durante la implementación sin necesidad de actualizar el código.</span><span class="sxs-lookup"><span data-stu-id="91c78-110">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="91c78-111">La sección de configuración Entity Framework</span><span class="sxs-lookup"><span data-stu-id="91c78-111">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="91c78-112">A partir de EF 4.1, podría establecer el inicializador de base de datos para un contexto mediante la sección **appSettings** del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="91c78-112">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="91c78-113">En EF 4,3, se presentó la sección **entityFramework** personalizada para controlar la nueva configuración.</span><span class="sxs-lookup"><span data-stu-id="91c78-113">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="91c78-114">Entity Framework seguirá reconociendo los inicializadores de base de datos establecidos con el formato antiguo, pero se recomienda pasar al nuevo formato siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="91c78-114">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="91c78-115">La sección **entityFramework** se agregó automáticamente al archivo de configuración del proyecto al instalar el paquete NuGet entityFramework.</span><span class="sxs-lookup"><span data-stu-id="91c78-115">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

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

## <a name="connection-strings"></a><span data-ttu-id="91c78-116">Cadenas de conexión</span><span class="sxs-lookup"><span data-stu-id="91c78-116">Connection Strings</span></span>  

<span data-ttu-id="91c78-117">En [esta página](xref:ef6/fundamentals/configuring/connection-strings) se proporcionan más detalles sobre cómo Entity Framework determina la base de datos que se va a usar, incluidas las cadenas de conexión en el archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="91c78-117">[This page](xref:ef6/fundamentals/configuring/connection-strings) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="91c78-118">Las cadenas de conexión van en el elemento **connectionStrings** estándar y no requieren la sección **entityFramework** .</span><span class="sxs-lookup"><span data-stu-id="91c78-118">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="91c78-119">Los modelos basados en Code First usan cadenas de conexión ADO.NET normales.</span><span class="sxs-lookup"><span data-stu-id="91c78-119">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="91c78-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="91c78-120">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="91c78-121">Los modelos basados en el diseñador EF usan cadenas de conexión EF especiales.</span><span class="sxs-lookup"><span data-stu-id="91c78-121">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="91c78-122">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="91c78-122">For example:</span></span>  

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

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="91c78-123">Tipo de configuración basada en código (EF6 en adelante)</span><span class="sxs-lookup"><span data-stu-id="91c78-123">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="91c78-124">A partir de EF6, puede especificar el DbConfiguration para EF que se va a usar para la [configuración basada en código](xref:ef6/fundamentals/configuring/code-based) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="91c78-124">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](xref:ef6/fundamentals/configuring/code-based) in your application.</span></span> <span data-ttu-id="91c78-125">En la mayoría de los casos, no es necesario especificar esta configuración, ya que EF detectará automáticamente el DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="91c78-125">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="91c78-126">Para obtener detalles sobre Cuándo es posible que tenga que especificar DbConfiguration en el archivo de configuración, consulte la sección **mover DbConfiguration** de [configuración basada en código](xref:ef6/fundamentals/configuring/code-based).</span><span class="sxs-lookup"><span data-stu-id="91c78-126">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span>  

<span data-ttu-id="91c78-127">Para establecer un tipo DbConfiguration, especifique el nombre del tipo calificado con el ensamblado en el elemento **codeConfigurationType** .</span><span class="sxs-lookup"><span data-stu-id="91c78-127">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="91c78-128">Un nombre completo de ensamblado es el nombre completo del espacio de nombres, seguido de una coma, el ensamblado en el que reside el tipo.</span><span class="sxs-lookup"><span data-stu-id="91c78-128">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="91c78-129">También puede especificar la versión del ensamblado, la referencia cultural y el token de clave pública.</span><span class="sxs-lookup"><span data-stu-id="91c78-129">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="91c78-130">Proveedores de bases de datos de EF (EF6 en adelante)</span><span class="sxs-lookup"><span data-stu-id="91c78-130">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="91c78-131">Antes de EF6, los elementos específicos de Entity Framework de un proveedor de base de datos debían incluirse como parte del proveedor ADO.NET básico.</span><span class="sxs-lookup"><span data-stu-id="91c78-131">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="91c78-132">A partir de EF6, las partes específicas de EF ahora se administran y registran por separado.</span><span class="sxs-lookup"><span data-stu-id="91c78-132">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="91c78-133">Normalmente no necesitará registrar los proveedores.</span><span class="sxs-lookup"><span data-stu-id="91c78-133">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="91c78-134">Esto lo realiza normalmente el proveedor cuando se instala.</span><span class="sxs-lookup"><span data-stu-id="91c78-134">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="91c78-135">Los proveedores se registran mediante la inclusión de un elemento de **proveedor** en la sección de **proveedores** secundarios de la sección **entityFramework** .</span><span class="sxs-lookup"><span data-stu-id="91c78-135">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="91c78-136">Hay dos atributos necesarios para una entrada de proveedor:</span><span class="sxs-lookup"><span data-stu-id="91c78-136">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="91c78-137">**invariantName** identifica el proveedor ADO.net principal al que se destina este proveedor EF</span><span class="sxs-lookup"><span data-stu-id="91c78-137">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="91c78-138">**Type** es el nombre de tipo calificado con el ensamblado de la implementación del proveedor EF</span><span class="sxs-lookup"><span data-stu-id="91c78-138">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="91c78-139">Un nombre completo de ensamblado es el nombre completo del espacio de nombres, seguido de una coma, el ensamblado en el que reside el tipo.</span><span class="sxs-lookup"><span data-stu-id="91c78-139">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="91c78-140">También puede especificar la versión del ensamblado, la referencia cultural y el token de clave pública.</span><span class="sxs-lookup"><span data-stu-id="91c78-140">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="91c78-141">Como ejemplo, aquí se muestra la entrada que se crea para registrar el proveedor de SQL Server predeterminado al instalar Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="91c78-141">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="91c78-142">Interceptores (EF 6.1 y versiones posteriores)</span><span class="sxs-lookup"><span data-stu-id="91c78-142">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="91c78-143">A partir de EF 6.1, puede registrar los interceptores en el archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="91c78-143">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="91c78-144">Los interceptores permiten ejecutar lógica adicional cuando EF realiza ciertas operaciones, como la ejecución de consultas de base de datos, la apertura de conexiones, etc.</span><span class="sxs-lookup"><span data-stu-id="91c78-144">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="91c78-145">Los interceptores se registran mediante la inclusión de un elemento **interceptor** en la sección de **interceptores** secundaria de la sección **entityFramework** .</span><span class="sxs-lookup"><span data-stu-id="91c78-145">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="91c78-146">Por ejemplo, la configuración siguiente registra el interceptor de **DatabaseLogger** integrado que registrará todas las operaciones de base de datos en la consola.</span><span class="sxs-lookup"><span data-stu-id="91c78-146">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="91c78-147">Registro de operaciones de base de datos en un archivo (EF 6.1 en adelante)</span><span class="sxs-lookup"><span data-stu-id="91c78-147">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="91c78-148">El registro de los interceptores mediante el archivo de configuración es especialmente útil si desea agregar el registro a una aplicación existente para ayudar a depurar un problema.</span><span class="sxs-lookup"><span data-stu-id="91c78-148">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="91c78-149">**DatabaseLogger** admite el registro en un archivo proporcionando el nombre de archivo como un parámetro de constructor.</span><span class="sxs-lookup"><span data-stu-id="91c78-149">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="91c78-150">De forma predeterminada, esto hará que el archivo de registro se sobrescriba con un nuevo archivo cada vez que se inicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="91c78-150">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="91c78-151">En su lugar, anexe al archivo de registro si ya existe, use algo parecido a:</span><span class="sxs-lookup"><span data-stu-id="91c78-151">To instead append to the log file if it already exists use something like:</span></span>  

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

<span data-ttu-id="91c78-152">Para obtener información adicional sobre **DatabaseLogger** y el registro de interceptores, consulte la entrada de blog [EF 6,1: activar el registro sin volver a compilar](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span><span class="sxs-lookup"><span data-stu-id="91c78-152">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="91c78-153">Code First factoría de conexión predeterminada</span><span class="sxs-lookup"><span data-stu-id="91c78-153">Code First Default Connection Factory</span></span>  

<span data-ttu-id="91c78-154">La sección configuración permite especificar un generador de conexiones predeterminado que Code First debe usar para buscar una base de datos que se utilizará para un contexto.</span><span class="sxs-lookup"><span data-stu-id="91c78-154">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="91c78-155">El generador de conexiones predeterminado solo se usa cuando no se ha agregado ninguna cadena de conexión al archivo de configuración para un contexto.</span><span class="sxs-lookup"><span data-stu-id="91c78-155">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="91c78-156">Al instalar el paquete de NuGet de EF, se registró una factoría de conexión predeterminada que señala a SQL Express o a LocalDB, en función de la que haya instalado.</span><span class="sxs-lookup"><span data-stu-id="91c78-156">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="91c78-157">Para establecer un generador de conexiones, especifique el nombre del tipo calificado con el ensamblado en el elemento **defaultConnectionFactory** .</span><span class="sxs-lookup"><span data-stu-id="91c78-157">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="91c78-158">Un nombre completo de ensamblado es el nombre completo del espacio de nombres, seguido de una coma, el ensamblado en el que reside el tipo.</span><span class="sxs-lookup"><span data-stu-id="91c78-158">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="91c78-159">También puede especificar la versión del ensamblado, la referencia cultural y el token de clave pública.</span><span class="sxs-lookup"><span data-stu-id="91c78-159">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="91c78-160">A continuación se muestra un ejemplo de configuración de su propio generador de conexiones predeterminado:</span><span class="sxs-lookup"><span data-stu-id="91c78-160">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="91c78-161">En el ejemplo anterior se requiere que el generador personalizado tenga un constructor sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="91c78-161">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="91c78-162">Si es necesario, puede especificar parámetros de constructor mediante el elemento **Parameters** .</span><span class="sxs-lookup"><span data-stu-id="91c78-162">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="91c78-163">Por ejemplo, SqlCeConnectionFactory, que se incluye en Entity Framework, requiere que proporcione un nombre invariable de proveedor al constructor.</span><span class="sxs-lookup"><span data-stu-id="91c78-163">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="91c78-164">El nombre invariable del proveedor identifica la versión de SQL Compact que quiere usar.</span><span class="sxs-lookup"><span data-stu-id="91c78-164">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="91c78-165">La configuración siguiente hará que los contextos usen SQL Compact versión 4,0 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="91c78-165">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="91c78-166">Si no establece un generador de conexiones predeterminado, Code First usa SqlConnectionFactory, que apunta a `.\SQLEXPRESS` .</span><span class="sxs-lookup"><span data-stu-id="91c78-166">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="91c78-167">SqlConnectionFactory también tiene un constructor que permite invalidar partes de la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="91c78-167">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="91c78-168">Si desea utilizar una instancia de SQL Server distinta de `.\SQLEXPRESS` , puede usar este constructor para establecer el servidor.</span><span class="sxs-lookup"><span data-stu-id="91c78-168">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="91c78-169">La configuración siguiente hará que Code First use **MyDatabaseServer** para los contextos que no tienen un conjunto de cadenas de conexión explícitas.</span><span class="sxs-lookup"><span data-stu-id="91c78-169">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="91c78-170">De forma predeterminada, se supone que los argumentos del constructor son del tipo cadena.</span><span class="sxs-lookup"><span data-stu-id="91c78-170">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="91c78-171">Puede usar el atributo type para cambiar esto.</span><span class="sxs-lookup"><span data-stu-id="91c78-171">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="91c78-172">Inicializadores de base de datos</span><span class="sxs-lookup"><span data-stu-id="91c78-172">Database Initializers</span></span>  

<span data-ttu-id="91c78-173">Los inicializadores de base de datos se configuran en función del contexto.</span><span class="sxs-lookup"><span data-stu-id="91c78-173">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="91c78-174">Se pueden establecer en el archivo de configuración mediante el elemento de **contexto** .</span><span class="sxs-lookup"><span data-stu-id="91c78-174">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="91c78-175">Este elemento usa el nombre completo del ensamblado para identificar el contexto que se está configurando.</span><span class="sxs-lookup"><span data-stu-id="91c78-175">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="91c78-176">De forma predeterminada, Code First contextos se configuran para usar el inicializador CreateDatabaseIfNotExists.</span><span class="sxs-lookup"><span data-stu-id="91c78-176">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="91c78-177">Hay un atributo **disableDatabaseInitialization** en el elemento de **contexto** que se puede usar para deshabilitar la inicialización de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="91c78-177">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="91c78-178">Por ejemplo, la siguiente configuración deshabilita la inicialización de la base de datos para el contexto blog. BlogContext definido en MyAssembly.dll.</span><span class="sxs-lookup"><span data-stu-id="91c78-178">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="91c78-179">Puede usar el elemento **databaseInitializer** para establecer un inicializador personalizado.</span><span class="sxs-lookup"><span data-stu-id="91c78-179">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="91c78-180">Los parámetros de constructor utilizan la misma sintaxis que los generadores de conexiones predeterminados.</span><span class="sxs-lookup"><span data-stu-id="91c78-180">Constructor parameters use the same syntax as default connection factories.</span></span>  

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

<span data-ttu-id="91c78-181">Puede configurar uno de los inicializadores de base de datos genéricos que se incluyen en Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="91c78-181">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="91c78-182">El atributo **Type** utiliza el formato de .NET Framework para los tipos genéricos.</span><span class="sxs-lookup"><span data-stu-id="91c78-182">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="91c78-183">Por ejemplo, si usa Migraciones de Code First, puede configurar la base de datos que se va a migrar automáticamente mediante el `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` inicializador.</span><span class="sxs-lookup"><span data-stu-id="91c78-183">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
