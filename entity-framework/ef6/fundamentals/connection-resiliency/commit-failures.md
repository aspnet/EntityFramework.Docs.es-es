---
title: Manejo de errores de confirmación de transacción - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434141"
---
# <a name="handling-transaction-commit-failures"></a>Manejo de errores de confirmación de transacción
> [!NOTE]
> **EF6.1 Solo en adelante:** las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 6.1. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

Como parte de 6.1, estamos introduciendo una nueva característica de resistencia de conexión para EF: la capacidad de detectar y recuperar automáticamente cuando los errores de conexión transitorios afectan al reconocimiento de confirmaciones de transacciones. Los detalles completos del escenario se describen mejor en la entrada de blog Conectividad de BASE de datos SQL y el problema de [idempotencia.](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)  En resumen, el escenario es que cuando se genera una excepción durante una confirmación de transacción hay dos causas posibles:  

1. Error en la confirmación de transacción en el servidor
2. La confirmación de transacción se realizó correctamente en el servidor, pero un problema de conectividad impidió que la notificación de éxito llegara al cliente  

Cuando ocurre la primera situación, la aplicación o el usuario puede volver a intentar la operación, pero cuando se produce la segunda situación se deben evitar los reintentos y la aplicación podría recuperarse automáticamente. El desafío es que sin la capacidad de detectar cuál fue la razón real por la que se notificó una excepción durante la confirmación, la aplicación no puede elegir el curso de acción correcto. La nueva característica de EF 6.1 permite a EF comprobar con la base de datos si la transacción se realizó correctamente y realizar el curso de acción correcto de forma transparente.  

## <a name="using-the-feature"></a>Uso de la característica  

Para habilitar la característica, debe incluir una llamada a [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) en el constructor de su **DbConfiguration**. Si no está familiarizado con **DbConfiguration**, consulte [Configuración basada en código](~/ef6/fundamentals/configuring/code-based.md). Esta característica se puede utilizar en combinación con los reintentos automáticos que introdujimos en EF6, que ayudan en la situación en la que la transacción realmente no se pudo confirmar en el servidor debido a un error transitorio:  

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

Cuando la característica está habilitada, EF agregará automáticamente una nueva tabla a la base de datos denominada **__Transactions**. Se inserta una nueva fila en esta tabla cada vez que EF crea una transacción y esa fila se comprueba si existe si se produce un error de transacción durante la confirmación.  

Aunque EF hará un mejor esfuerzo para podar filas de la tabla cuando ya no se necesitan, la tabla puede crecer si la aplicación se cierra prematuramente y, por ese motivo, es posible que deba purgar la tabla manualmente en algunos casos.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Cómo gestionar errores de confirmación con versiones anteriores

Antes de EF 6.1 no había mecanismo para controlar errores de confirmación en el producto EF. Hay varias maneras de abordar esta situación que se pueden aplicar a versiones anteriores de EF6:  

* Opción 1 - No hacer nada  

  La probabilidad de que se produzca un error de conexión durante la confirmación de la transacción es baja, por lo que puede ser aceptable que la aplicación solo falle si se produce realmente esta condición.  

* Opción 2 - Utilice la base de datos para restablecer el estado  

  1. Descartar el DbContext actual  
  2. Cree un nuevo DbContext y restaure el estado de la aplicación desde la base de datos  
  3. Informar al usuario de que es posible que la última operación no se haya completado correctamente  

* Opción 3 - Seguimiento manual de la transacción  

  1. Agregue una tabla sin seguimiento a la base de datos utilizada para realizar un seguimiento del estado de las transacciones.  
  2. Inserte una fila en la tabla al principio de cada transacción.  
  3. Si se produce un error en la conexión durante la confirmación, compruebe la presencia de la fila correspondiente en la base de datos.  
     - Si la fila está presente, continúe normalmente, ya que la transacción se ha confirmado correctamente  
     - Si la fila está ausente, utilice una estrategia de ejecución para reintentar la operación actual.  
  4. Si la confirmación se realiza correctamente, elimine la fila correspondiente para evitar el crecimiento de la tabla.  

[Esta entrada](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) de blog contiene código de ejemplo para realizar esto en SQL Azure.This blog post contains sample code for accomplish this on SQL Azure.  
