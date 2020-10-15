---
title: 'Control de errores de confirmación de transacción: EF6'
description: Control de errores de confirmación de transacción en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: ecc9daf568f12e3bf93af8c93e543c12dfddbb06
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065594"
---
# <a name="handling-transaction-commit-failures"></a>Control de errores de confirmación de transacción

> [!NOTE]
> **EF 6.1 en adelante solo** : las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 6,1. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

Como parte de 6,1 estamos introduciendo una nueva característica de resistencia de conexión para EF: la capacidad de detectar y recuperar automáticamente cuando los errores de conexión transitorios afecten a la confirmación de confirmaciones de transacción. Los detalles completos del escenario se describen mejor en la entrada de blog [SQL Database la conectividad y el problema de Idempotencia](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).  En Resumen, el escenario es que, cuando se produce una excepción durante la confirmación de una transacción, hay dos causas posibles:  

1. Error en la confirmación de la transacción en el servidor
2. La transacción se confirmó correctamente en el servidor pero un problema de conectividad impidió que la notificación de éxito llegara al cliente  

Cuando se produce la primera situación de la aplicación o el usuario puede reintentar la operación, pero cuando se produce la segunda situación, se deben evitar los reintentos y la aplicación podría recuperarse automáticamente. El reto es que sin la capacidad de detectar cuál fue el motivo real en el que se comunicó una excepción durante la confirmación, la aplicación no puede elegir el curso de acción correcto. La nueva característica de EF 6,1 permite a EF volver a comprobar con la base de datos si la transacción se ha realizado correctamente y realizar el curso adecuado de acción de forma transparente.  

## <a name="using-the-feature"></a>Uso de la característica  

Para habilitar la característica que necesita, incluya una llamada a [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) en el constructor de su **DbConfiguration**. Si no está familiarizado con **DbConfiguration**, consulte [configuración basada en código](xref:ef6/fundamentals/configuring/code-based). Esta característica se puede usar en combinación con los reintentos automáticos que se introdujeron en EF6, que ayudan en la situación en la que la transacción realmente no pudo confirmarse en el servidor debido a un error transitorio:  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a>Cómo se realiza el seguimiento de las transacciones  

Cuando la característica está habilitada, EF agregará automáticamente una nueva tabla a la base de datos denominada **__Transactions**. En esta tabla se inserta una nueva fila cada vez que EF crea una transacción y se comprueba la existencia de esa fila si se produce un error de transacción durante la confirmación.  

Aunque EF realizará el mejor esfuerzo para eliminar filas de la tabla cuando ya no se necesiten, la tabla puede crecer si la aplicación se cierra prematuramente y, por ese motivo, es posible que necesite purgar la tabla manualmente en algunos casos.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Cómo controlar los errores de confirmación con versiones anteriores

Antes de EF 6,1 no había ningún mecanismo para controlar los errores de confirmación en el producto EF. Hay varias maneras de solucionar esta situación que se pueden aplicar a versiones anteriores de EF6:  

* Opción 1: no hacer nada  

  La probabilidad de que se produzca un error de conexión durante la confirmación de la transacción es baja, por lo que puede ser aceptable que la aplicación no funcione correctamente si esta condición se produce realmente.  

* Opción 2: usar la base de datos para restablecer el estado  

  1. Descartar el DbContext actual  
  2. Crear un nuevo DbContext y restaurar el estado de la aplicación desde la base de datos  
  3. Informe al usuario de que es posible que la última operación no se haya completado correctamente  

* Opción 3: realizar un seguimiento manual de la transacción  

  1. Agregue una tabla sin seguimiento a la base de datos utilizada para realizar el seguimiento del estado de las transacciones.  
  2. Inserte una fila en la tabla al principio de cada transacción.  
  3. Si se produce un error en la conexión durante la confirmación, Compruebe la presencia de la fila correspondiente en la base de datos.  
     * Si la fila está presente, continúe normalmente, ya que la transacción se confirmó correctamente.  
     * Si la fila no existe, use una estrategia de ejecución para volver a intentar la operación actual.  
  4. Si la confirmación se realiza correctamente, elimine la fila correspondiente para evitar el crecimiento de la tabla.  

[Esta entrada de blog](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contiene código de ejemplo para realizar esta instalación en SQL Azure.  
