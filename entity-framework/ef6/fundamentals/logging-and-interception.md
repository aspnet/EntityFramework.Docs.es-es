---
title: 'Registro e intercepción de operaciones de base de datos: EF6'
description: Registrar e interceptar operaciones de base de datos en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
uid: ef6/fundamentals/logging-and-interception
ms.openlocfilehash: bb5c3392b4f2e1f291d7ac373d07724f56d0eb30
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616194"
---
# <a name="logging-and-intercepting-database-operations"></a>Registrar e interceptar operaciones de base de datos
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

A partir de Entity Framework 6, en cualquier momento Entity Framework envía un comando a la base de datos. este comando puede ser interceptado por el código de la aplicación. Esto se usa normalmente para registrar SQL, pero también se puede usar para modificar o anular el comando.  

En concreto, EF incluye:  

- Una propiedad de registro para el contexto similar a DataContext. log en LINQ to SQL  
- Mecanismo para personalizar el contenido y el formato de la salida enviada al registro.  
- Bloques de creación de bajo nivel para la interceptación, lo que ofrece un mayor control y flexibilidad  

## <a name="context-log-property"></a>Propiedad de registro de contexto  

La propiedad DbContext. Database. log se puede establecer en un delegado para cualquier método que toma una cadena. Normalmente, se usa con cualquier TextWriter estableciéndolo en el método "write" de ese TextWriter. Todos los SQL generados por el contexto actual se registrarán en ese escritor. Por ejemplo, el código siguiente registrará SQL en la consola:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

Tenga en cuenta ese contexto. Database. log se establece en Console. Write. Esto es todo lo que se necesita para registrar SQL en la consola.  

Vamos a agregar algunos códigos simples de consulta/inserción/actualización para que podamos ver algunos resultados:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

Se generará el siguiente resultado:  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

(Tenga en cuenta que esta es la salida, suponiendo que ya se ha producido cualquier inicialización de base de datos. Si aún no se ha producido la inicialización de la base de datos, habría mucha más salida mostrando todas las migraciones de trabajo en segundo plano para comprobar o crear una nueva base de datos.  

## <a name="what-gets-logged"></a>¿Qué se registra?  

Cuando se establece la propiedad log, se registran todos los elementos siguientes:  

- SQL para todos los tipos de comandos diferentes. Por ejemplo:  
    - Consultas, incluidas las consultas LINQ normales, las consultas eSQL y las consultas sin formato de métodos como SqlQuery  
    - Inserciones, actualizaciones y eliminaciones generadas como parte de SaveChanges  
    - Consultas de carga de relaciones como las generadas por la carga diferida  
- Parámetros  
- Si el comando se ejecuta de forma asincrónica o no  
- Marca de tiempo que indica cuándo empezó A ejecutarse el comando.  
- Si el comando se completó correctamente, se produjo un error al iniciar una excepción o, para Async, se canceló.  
- Alguna indicación del valor del resultado  
- Cantidad aproximada de tiempo que se tardó en ejecutar el comando. Tenga en cuenta que es el momento en el que se envía el comando para volver a obtener el objeto de resultado. No incluye el tiempo para leer los resultados.  

Al examinar la salida de ejemplo anterior, cada uno de los cuatro comandos registrados son:  

- La consulta que resulta de la llamada a context. Blogs. First  
    - Tenga en cuenta que el método ToString para obtener el SQL no habría funcionado para esta consulta porque "First" no proporciona un IQueryable en el que se podría llamar a ToString  
- La consulta que resulta de la carga diferida del blog. Publique  
    - Observe los detalles del parámetro para el valor de clave para el que se está produciendo la carga diferida.  
    - Solo se registran las propiedades del parámetro que se establecen en valores no predeterminados. Por ejemplo, la propiedad Size solo se muestra si es distinto de cero.  
- Dos comandos resultantes de SaveChangesAsync; uno para la actualización para cambiar el título de una publicación, el otro para una inserción para agregar una nueva publicación  
    - Tenga en cuenta los detalles de los parámetros de las propiedades de FK y title  
    - Tenga en cuenta que estos comandos se ejecutan de forma asincrónica  

## <a name="logging-to-different-places"></a>Registro en distintos lugares  

Como se indicó anteriormente, el registro en la consola es muy sencillo. También es fácil de registrar en la memoria, el archivo, etc. mediante el uso de diferentes tipos de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).  

Si está familiarizado con LINQ to SQL podría observar que en LINQ to SQL la propiedad log está establecida en el objeto TextWriter real (por ejemplo, Console. out) mientras que en EF la propiedad log está establecida en un método que acepta una cadena (por ejemplo, Console. Write o Console. out. Write). El motivo es desacoplar EF de TextWriter mediante la aceptación de cualquier delegado que pueda actuar como receptor de cadenas. Por ejemplo, Imagine que ya tiene alguna plataforma de registro y que define un método de registro como el siguiente:  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

Se puede enlazar a la propiedad de registro EF de la siguiente manera:  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>Registro de resultados  

El registrador predeterminado registra el texto del comando (SQL), los parámetros y la línea "en ejecución" con una marca de tiempo antes de que el comando se envíe a la base de datos. Una línea "completada" que contiene el tiempo transcurrido se registra después de la ejecución del comando.  

Tenga en cuenta que para los comandos asincrónicos, la línea "completada" no se registra hasta que la tarea asincrónica se complete realmente, se produzca un error o se cancele.  

La línea "completado" contiene información diferente en función del tipo de comando y de si la ejecución se realizó correctamente o no.  

### <a name="successful-execution"></a>Ejecución correcta  

En el caso de los comandos que se completan correctamente, la salida es "Completed in x MS with result:" seguida de alguna indicación de cuál fue el resultado. Para los comandos que devuelven un lector de datos, la indicación de resultado es el tipo de [DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx) devuelto. En el caso de los comandos que devuelven un valor entero como el comando UPDATE mostrado sobre el resultado que se muestra es ese entero.  

### <a name="failed-execution"></a>Error de ejecución  

En el caso de los comandos que producen un error iniciando una excepción, la salida contiene el mensaje de la excepción. Por ejemplo, el uso de SqlQuery para realizar consultas en una tabla que existe producirá una salida de registro similar a la siguiente:  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>Ejecución cancelada  

En el caso de los comandos Async en los que se cancela la tarea, el resultado podría ser un error con una excepción, ya que esto es lo que suele hacer el proveedor ADO.NET subyacente cuando se intenta cancelar. Si esto no ocurre y la tarea se cancela correctamente, la salida tendrá un aspecto similar al siguiente:  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>Cambiar el contenido y el formato del registro  

En, la propiedad Database. log hace uso de un objeto DatabaseLogFormatter. Este objeto enlaza eficazmente una implementación de IDbCommandInterceptor (consulte a continuación) a un delegado que acepta cadenas y DbContext. Esto significa que se llama a los métodos de DatabaseLogFormatter antes y después de la ejecución de comandos por EF. Estos métodos de DatabaseLogFormatter recopilan y dan formato al resultado del registro y lo envían al delegado.  

### <a name="customizing-databaselogformatter"></a>Personalización de DatabaseLogFormatter  

El cambio de lo que se registra y su formato se puede lograr creando una nueva clase que se deriva de DatabaseLogFormatter e invalida los métodos según corresponda. Los métodos más comunes para invalidar son:  

- LogCommand: invalide esto para cambiar el modo en que se registran los comandos antes de que se ejecuten. De forma predeterminada, LogCommand llama a LogParameter para cada parámetro; en su lugar, puede elegir hacer lo mismo en los parámetros de invalidación o de control de forma diferente.  
- LogResult: invalide esto para cambiar el modo en que se registra el resultado de la ejecución de un comando.  
- LogParameter: invalide esto para cambiar el formato y el contenido del registro de parámetros.  

Por ejemplo, supongamos que deseamos registrar solo una línea antes de que cada comando se envíe a la base de datos. Esto puede hacerse con dos invalidaciones:  

- Invalide LogCommand para dar formato y escribir la única línea de SQL  
- Invalide LogResult para no hacer nada.  

El código debería tener un aspecto parecido al siguiente:

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

Para registrar la salida, simplemente llame al método Write, que enviará la salida al delegado de escritura configurado.  

(Tenga en cuenta que este código hace la eliminación simplista de los saltos de línea como ejemplo. Lo más probable es que no funcione bien para ver SQL complejo).  

### <a name="setting-the-databaselogformatter"></a>Establecer DatabaseLogFormatter  

Una vez que se ha creado una nueva clase DatabaseLogFormatter, debe registrarse con EF. Esto se hace mediante la configuración basada en código. En pocas palabras, esto significa que se crea una nueva clase que se deriva de DbConfiguration en el mismo ensamblado que la clase DbContext y, después, se llama a SetDatabaseLogFormatter en el constructor de esta nueva clase. Por ejemplo:  

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>Usar el nuevo DatabaseLogFormatter  

Este nuevo DatabaseLogFormatter se usará siempre que se establezca Database. log. Por lo tanto, la ejecución del código de la parte 1 generará ahora el siguiente resultado:  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>Bloques de creación de interceptación  

Hasta ahora hemos visto cómo usar DbContext. Database. log para registrar el SQL generado por EF. Pero este código es realmente una fachada relativamente fina sobre algunos bloques de creación de bajo nivel para la interceptación más general.  

### <a name="interception-interfaces"></a>Interfaces de interceptación  

El código de intercepción se crea en torno al concepto de interfaces de interceptación. Estas interfaces heredan de IDbInterceptor y definen métodos a los que se llama cuando EF realiza alguna acción. La intención es tener una interfaz por cada tipo de objeto que se va a interceptar. Por ejemplo, la interfaz IDbCommandInterceptor define métodos a los que se llama antes de que EF realice una llamada a ExecuteNonQuery, ExecuteScalar, ExecuteReader y a los métodos relacionados. Del mismo modo, la interfaz define métodos a los que se llama cuando se completa cada una de estas operaciones. La clase DatabaseLogFormatter que hemos examinado anteriormente implementa esta interfaz para registrar los comandos.  

### <a name="the-interception-context"></a>El contexto de interceptación  

Si se examinan los métodos definidos en cualquiera de las interfaces del interceptor, es evidente que cada llamada recibe un objeto de tipo DbInterceptionContext o algún tipo derivado de este, como DbCommandInterceptionContext \<\> . Este objeto contiene información contextual sobre la acción que está llevando a cabo EF. Por ejemplo, si la acción se realiza en nombre de un DbContext, el DbContext se incluye en el DbInterceptionContext. De forma similar, para los comandos que se ejecutan de forma asincrónica, la marca IsAsync se establece en DbCommandInterceptionContext.  

### <a name="result-handling"></a>Control de resultados  

La \<\> clase DbCommandInterceptionContext contiene una propiedad denominada result, OriginalResult, Exception y OriginalException. Estas propiedades se establecen en NULL/Zero para las llamadas a los métodos de interceptación a los que se llama antes de que se ejecute la operación, es decir, para... Ejecutar métodos. Si la operación se ejecuta y se realiza correctamente, result y OriginalResult se establecen en el resultado de la operación. Estos valores se pueden observar en los métodos de interceptación a los que se llama después de que se haya ejecutado la operación, es decir, en... Métodos ejecutados. Del mismo modo, si se produce la operación, se establecerán las propiedades Exception y OriginalException.  

#### <a name="suppressing-execution"></a>Suprimir la ejecución  

Si un interceptor establece la propiedad de resultado antes de que se ejecute el comando (en una de las propiedades... Ejecutar métodos) entonces EF no intentará realmente ejecutar el comando, sino que solo usará el conjunto de resultados. En otras palabras, el interceptor puede suprimir la ejecución del comando, pero el EF continúa como si se hubiera ejecutado el comando.  

Un ejemplo de cómo se podría usar esto es el procesamiento por lotes de comandos que se ha realizado tradicionalmente con un proveedor de ajuste. El interceptor almacenaría el comando para su ejecución posterior como un lote, pero "fingiría" a EF que el comando se ejecutaba como normal. Tenga en cuenta que esto requiere más que esto para implementar el procesamiento por lotes, pero este es un ejemplo de cómo se puede usar el resultado de la interceptación.  

La ejecución también se puede suprimir si se establece la propiedad Exception en una de las propiedades... Ejecutar métodos. Esto hace que EF continúe como si se produjera un error en la ejecución de la operación produciendo la excepción dada. Por supuesto, esto puede hacer que la aplicación se bloquee, pero también puede ser una excepción transitoria o cualquier otra excepción controlada por EF. Por ejemplo, se puede usar en entornos de prueba para probar el comportamiento de una aplicación cuando se produce un error en la ejecución del comando.  

#### <a name="changing-the-result-after-execution"></a>Cambiar el resultado después de la ejecución  

Si un interceptor establece la propiedad de resultado después de que se haya ejecutado el comando (en una de las propiedades... Métodos ejecutados) entonces EF usará el resultado cambiado en lugar del resultado que se devolvió realmente desde la operación. Del mismo modo, si un interceptor establece la propiedad Exception después de que se haya ejecutado el comando, EF producirá la excepción set como si la operación hubiera producido la excepción.  

Un interceptor también puede establecer la propiedad Exception en null para indicar que no se debe producir ninguna excepción. Esto puede ser útil si se produce un error en la ejecución de la operación, pero el interceptor quiere que EF continúe como si la operación se hubiera realizado correctamente. Normalmente, esto también implica establecer el resultado para que EF tenga algún valor de resultado con el que trabajar mientras continúa.  

#### <a name="originalresult-and-originalexception"></a>OriginalResult y OriginalException  

Después de que EF haya ejecutado una operación, establecerá las propiedades result y OriginalResult si la ejecución no produjo un error, o bien las propiedades Exception y OriginalException si se produce un error de ejecución con una excepción.  

Las propiedades OriginalResult y OriginalException son de solo lectura y solo las establece EF después de ejecutar realmente una operación. Los interceptores no pueden establecer estas propiedades. Esto significa que cualquier interceptor puede distinguir entre una excepción o un resultado establecido por otro interceptor, en lugar de la excepción real o el resultado que se produjo cuando se ejecutó la operación.  

### <a name="registering-interceptors"></a>Registro de interceptores  

Una vez que se ha creado una clase que implementa una o varias interfaces de interceptación, se puede registrar con EF mediante la clase DbInterception. Por ejemplo:  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

Los interceptores también se pueden registrar en el nivel de dominio de aplicación mediante el mecanismo de configuración basado en código DbConfiguration.  

### <a name="example-logging-to-nlog"></a>Ejemplo: registro en NLog  

Vamos a reunir todo esto en un ejemplo que usa IDbCommandInterceptor y [NLog](https://nlog-project.org/) para:  

- Registrar una advertencia para cualquier comando que se ejecute de forma no asincrónica  
- Registrar un error para cualquier comando que se produzca cuando se ejecute  

Esta es la clase que realiza el registro, que se debe registrar como se muestra arriba:  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

Observe cómo este código utiliza el contexto de interceptación para detectar cuándo un comando se ejecuta de forma no asincrónica y para detectar cuándo se produjo un error al ejecutar un comando.  
