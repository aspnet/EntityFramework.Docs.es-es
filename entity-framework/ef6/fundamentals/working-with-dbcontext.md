---
title: 'Trabajo con DbContext: EF6'
description: Trabajo con DbContext en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 7845d401cb0b8910cbfbba80eca2fd098c051b7d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618212"
---
# <a name="working-with-dbcontext"></a>Trabajar con DbContext

Para usar Entity Framework para consultar, insertar, actualizar y eliminar datos mediante objetos .NET, primero debe [crear un modelo](xref:ef6/modeling/index) que asigne las entidades y relaciones que se definen en el modelo a las tablas de una base de datos.

Una vez que tiene un modelo, la clase principal con la que interactúa la aplicación es `System.Data.Entity.DbContext` (a menudo se conoce como clase de contexto). Puede usar un DbContext asociado a un modelo para:
- Escribir y ejecutar consultas   
- Materializar los resultados de la consulta como objetos entidad
- Realizar un seguimiento de los cambios que se realizan en esos objetos
- Volver a guardar los cambios de objetos en la base de datos
- Enlazar objetos en memoria a controles de interfaz de usuario

En esta página se proporcionan instrucciones sobre cómo administrar la clase de contexto.  

## <a name="defining-a-dbcontext-derived-class"></a>Definir una clase derivada de DbContext  

La manera recomendada de trabajar con el contexto es definir una clase que derive de DbContext y exponga propiedades DbSet que representen colecciones de las entidades especificadas en el contexto. Si está trabajando con el diseñador de EF, el contexto se generará automáticamente. Si está trabajando con Code First, normalmente escribirá el contexto.  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

Una vez que tenga un contexto, debe consultar, agregar (mediante `Add` métodos o `Attach` ) o quitar (mediante `Remove` ) entidades en el contexto mediante estas propiedades. El acceso a una `DbSet` propiedad en un objeto de contexto representa una consulta de inicio que devuelve todas las entidades del tipo especificado. Tenga en cuenta que, al tener acceso a una propiedad, no se ejecutará la consulta. Una consulta se ejecuta cuando:  

- Se enumera mediante una instrucción `foreach` (C#) o `For Each` (Visual Basic).  
- Se enumera mediante una operación de colección como `ToArray` , `ToDictionary` o `ToList` .  
- Los operadores de LINQ como `First` o `Any` se especifican en la parte más externa de la consulta.  
- Se llama a uno de los métodos siguientes: el `Load` método de extensión, `DbEntityEntry.Reload` ,  `Database.ExecuteSqlCommand` y `DbSet<T>.Find` , si una entidad con la clave especificada no se encuentra ya cargada en el contexto.  

## <a name="lifetime"></a>Período de duración  

La duración del contexto comienza cuando se crea la instancia y finaliza cuando la instancia se desecha o se recolecta como elemento no utilizado. Use el **uso** de si desea que todos los recursos que controla el contexto se eliminen al final del bloque. Cuando se usa **con**, el compilador crea automáticamente un bloque try/finally y llama a Dispose en el bloque **Finally** .  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

Estas son algunas directrices generales a la hora de decidir la duración del contexto:  

- Al trabajar con aplicaciones Web, use una instancia de contexto por solicitud.  
- Al trabajar con Windows Presentation Foundation (WPF) o Windows Forms, utilice una instancia de contexto por formulario. Esto le permite usar la funcionalidad de seguimiento de cambios que proporciona el contexto.  
- Si la instancia de contexto la crea un contenedor de inserción de dependencias, suele ser responsabilidad del contenedor desechar el contexto.
- Si el contexto se crea en el código de la aplicación, no olvide eliminar el contexto cuando ya no sea necesario.  
- Al trabajar con un contexto de ejecución prolongada, tenga en cuenta lo siguiente:  
    - A medida que se cargan más objetos y sus referencias en la memoria, el consumo de memoria del contexto puede aumentar rápidamente. lo que puede ocasionar problemas de rendimiento.  
    - El contexto no es seguro para subprocesos, por lo que no debe compartirse entre varios subprocesos que realizan trabajo en él simultáneamente.
    - Si una excepción hace que el contexto esté en un estado irrecuperable, toda la aplicación puede finalizar.  
    - Las posibilidades de que haya problemas relacionados con la simultaneidad se incrementan a medida que aumenta la distancia entre el momento en que se consultan los datos y el momento en que se actualizan.  

## <a name="connections"></a>Conexiones  

De forma predeterminada, el contexto administra las conexiones a la base de datos. El contexto abre y cierra las conexiones según sea necesario. Por ejemplo, el contexto abre una conexión para ejecutar una consulta y, a continuación, cierra la conexión cuando se han procesado todos los conjuntos de resultados.  

Hay casos en los que se desea más control sobre el momento en que se abre y cierra la conexión. Por ejemplo, al trabajar con SQL Server Compact, a menudo se recomienda mantener una conexión abierta independiente con la base de datos mientras dure la aplicación para mejorar el rendimiento. Puede administrar este proceso manualmente mediante la propiedad `Connection`.  
