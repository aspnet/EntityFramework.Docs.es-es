---
title: 'Modelado de rendimiento: EF Core'
description: Modelado eficaz al usar Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/modeling-for-performance
ms.openlocfilehash: 882398189cc828798c1682f849fac524d90d317f
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023866"
---
# <a name="modeling-for-performance"></a>Modelado de rendimiento

En muchos casos, la manera de modelar puede tener un impacto profundo en el rendimiento de la aplicación. Aunque un modelo "correcto" y normalizado correctamente es normalmente un buen punto de partida, en las aplicaciones reales, algunos peligros pragmáticos pueden llevar mucho tiempo para lograr un buen rendimiento. Dado que es bastante difícil cambiar el modelo una vez que una aplicación se ejecuta en producción, merece la pena tener en cuenta el rendimiento al crear el modelo inicial.

## <a name="denormalization-and-caching"></a>Desnormalización y almacenamiento en caché

La *desnormalización* es la práctica de agregar datos redundantes al esquema, normalmente con el fin de eliminar combinaciones al realizar consultas. Por ejemplo, para un modelo con blogs y publicaciones, donde cada publicación tiene una clasificación, es posible que se le pida que muestre con frecuencia la clasificación promedio del blog. El enfoque sencillo para esto agruparía las entradas por su blog y calcularía el promedio como parte de la consulta. pero esto requiere una combinación costosa entre las dos tablas. La desnormalización agregaría el promedio calculado de todos los envíos a una nueva columna en el blog, de modo que se pueda acceder a él de inmediato, sin unirse ni calcular.

Lo anterior se puede ver como una forma de información de agregación de *almacenamiento en caché* de las publicaciones se almacena en caché en su blog. y, al igual que con cualquier almacenamiento en caché, el problema es cómo mantener actualizado el valor almacenado en caché con los datos que almacena en memoria caché. En muchos casos, es correcto que los datos almacenados en caché se retrasen un poco. por ejemplo, en el ejemplo anterior, suele ser razonable que la puntuación media del blog no esté totalmente actualizada en un momento dado. Si ese es el caso, puede hacer que se vuelva a calcular cada ahora y, después, a; de lo contrario, se debe configurar un sistema más elaborado para mantener actualizados los valores almacenados en caché.

A continuación se detallan algunas técnicas para la desnormalización y el almacenamiento en caché en EF Core y se apunta a las secciones pertinentes de la documentación.

### <a name="stored-computed-columns"></a>Columnas calculadas almacenadas

Si los datos que se van a almacenar en caché son un producto de otras columnas de la misma tabla, una [columna calculada almacenada](xref:core/modeling/generated-properties#computed-columns) puede ser una solución perfecta. Por ejemplo, un `Customer` puede tener `FirstName` `LastName` columnas y, pero es posible que sea necesario buscar por el *nombre completo* del cliente. La base de datos mantiene automáticamente una columna calculada almacenada, que la vuelve a calcular cada vez que se cambia la fila, e incluso puede definir un índice para acelerar las consultas.

### <a name="update-cache-columns-when-inputs-change"></a>Actualizar columnas de caché cuando cambien las entradas

Si la columna almacenada en caché necesita hacer referencia a entradas desde fuera de la fila de la tabla, no se pueden usar columnas calculadas. Sin embargo, sigue siendo posible volver a calcular la columna cada vez que cambie la entrada; por ejemplo, podría recalcular el promedio de clasificación del blog cada vez que se cambia, agrega o quita una publicación. Asegúrese de identificar las condiciones exactas en las que es necesario volver a calcular; de lo contrario, el valor almacenado en caché no se sincronizará.

Una forma de hacerlo consiste en realizar la actualización por su cuenta, a través de la API de EF Core normal. `SaveChanges`[Los eventos](xref:core/logging-events-diagnostics/events) o los [interceptores](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) se pueden usar para comprobar automáticamente si se está actualizando cualquier publicación y para realizar el recálculo de este modo. Tenga en cuenta que esto normalmente conlleva viajes de ida de base de datos adicionales, ya que se deben enviar comandos adicionales.

En el caso de las aplicaciones más sensibles al rendimiento, se pueden definir desencadenadores de base de datos para realizar automáticamente el recálculo en la base de datos. Esto guarda el interactivo de la base de datos adicional, se produce automáticamente dentro de la misma transacción que la actualización principal y puede ser más fácil de configurar. EF no proporciona ninguna API específica para crear o mantener desencadenadores, pero es perfecta para [crear una migración vacía y agregar la definición del desencadenador a través de SQL sin procesar](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

### <a name="materialized-views"></a>Vistas materializadas

Las vistas materializadas son similares a las vistas normales, excepto en que sus datos se almacenan en el disco ("materializado"), en lugar de calcularse cada vez que se consulta la vista. Esta herramienta es útil cuando no se desea simplemente agregar una única columna de caché a una base de datos existente, sino que se desea almacenar en caché todo el conjunto de resultados de los resultados de una consulta complicada y costosa, como si fuera una tabla normal. Estos resultados se pueden consultar de forma muy barata sin que se produzca ningún cálculo o combinación. A diferencia de las columnas calculadas, las vistas materializadas no se actualizan automáticamente cuando se modifican sus tablas subyacentes, sino que se deben actualizar manualmente. Si los datos almacenados en caché se pueden retrasar, la actualización de la vista se puede realizar a través de un temporizador. otra opción consiste en configurar desencadenadores de base de datos para revisar una vista materializada cuando se produzcan determinados eventos de base de datos.

EF no proporciona actualmente ninguna API específica para crear o mantener vistas, materializadas o de otro tipo. pero es perfectamente adecuado [crear una migración vacía y agregar la definición de la vista a través de SQL sin procesar](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

## <a name="inheritance-mapping"></a>Asignación de herencia

Se recomienda leer [la página dedicada en la herencia](xref:core/modeling/inheritance) antes de continuar con esta sección.

EF Core admite actualmente dos técnicas para asignar un modelo de herencia a una base de datos relacional:

* **Tabla por jerarquía** (TPH), en la que toda una jerarquía de clases de .net está asignada a una sola tabla de base de datos
* **Tabla por tipo** (TPT), en la que cada tipo de la jerarquía de .net se asigna a una tabla diferente en la base de datos.

La elección de la técnica de asignación de herencia puede tener un impacto considerable en el rendimiento de la aplicación; se recomienda medir cuidadosamente antes de confirmar una opción.

A veces, los usuarios eligen TPT porque parece ser la técnica de "limpiador"; una tabla independiente para cada tipo .NET hace que el esquema de la base de datos sea similar a la jerarquía de tipos .NET. Además, dado que TPH debe representar toda la jerarquía en una sola tabla, las filas tienen *todas* las columnas independientemente del tipo que realmente se mantiene en la fila, y las columnas no relacionadas siempre están vacías y no se utilizan. Además de parecerse a una técnica de asignación "sin limpieza", muchos consideran que estas columnas vacías ocupan un espacio considerable en la base de datos y pueden afectar también al rendimiento.

Sin embargo, la medición muestra que TPT en la mayoría de los casos es la técnica de asignación inferior desde un punto de vista del rendimiento. Cuando todos los datos de TPH proceden de una sola tabla, las consultas de TPT deben combinarse con varias tablas y las combinaciones son una de las principales fuentes de problemas de rendimiento de las bases de datos relacionales. Las bases de datos también suelen tratar bien con columnas vacías, y las características como [SQL Server columnas dispersas](/sql/relational-databases/tables/use-sparse-columns) pueden reducir aún más esta sobrecarga.

Para ver un ejemplo concreto, [vea este criterio de referencia](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/Inheritance.cs) que configura un modelo simple con una jerarquía de 7 tipos; 5000 las filas se inicializan para cada tipo: total de 35000 filas, y la prueba comparativa simplemente carga todas las filas de la base de datos:

| Método |     Media |   Error |  StdDev |     Gen. 0 |     Gen. 1 |     Gen. 2 | Allocated |
|------- |---------:|--------:|--------:|----------:|----------:|----------:|----------:|
|    TPH | 132,3 MS | 2,29 MS | 2,03 MS | 8000,0000 | 3000,0000 | 1250,0000 |  44,49 MB |
|    TPT | 201,3 MS | 3,32 ms | 3,10 ms | 9000,0000 | 4000,0000 |         - |  61,84 MB |

Como se puede ver, TPH es considerablemente más eficiente que TPT para este escenario. Tenga en cuenta que los resultados reales siempre dependen de la consulta específica que se ejecuta y el número de tablas de la jerarquía, por lo que otras consultas pueden mostrar un intervalo de rendimiento diferente. le recomendamos que use este código de referencia comparativa como plantilla para probar otras consultas.
