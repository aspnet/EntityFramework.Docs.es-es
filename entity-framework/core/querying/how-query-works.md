---
title: 'Funcionamiento de las consultas: EF Core'
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e8a50efe31468ea8df211602636dd474550bc0ef
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136240"
---
# <a name="how-queries-work"></a>Funcionamiento de las consultas

Entity Framework Core usa Language Integrated Query (LINQ) para consultar datos de la base de datos. LINQ permite usar C# (o el lenguaje .NET que prefiera) para escribir consultas fuertemente tipadas basadas en el contexto derivado y las clases de entidad.

## <a name="the-life-of-a-query"></a>La duración de una consulta

A continuación se muestra información general de alto nivel del proceso por el que pasa toda consulta.

1. Entity Framework Core procesa la consulta LINQ para crear una representación que está lista para que el proveedor de base de datos la procese.
   1. El resultado se almacena en caché para que no sea necesario hacer este procesamiento cada vez que se ejecuta la consulta.
2. El resultado se pasa al proveedor de base de datos.
   1. El proveedor de base de datos identifica qué partes de la consulta se pueden evaluar en la base de datos.
   2. Estas partes de la consulta se traducen al lenguaje de la consulta específico para la base de datos (por ejemplo, SQL para una base de datos relacional).
   3. Se envía una consulta a la base de datos y se devuelve el conjunto de resultados (los resultados son valores de la base de datos y no instancias de entidad).
3. Para cada elemento del conjunto de resultados
   1. Si se trata de una consulta con seguimiento, EF comprueba si los datos representan una entidad que ya existe en la herramienta de seguimiento de cambios para la instancia de contexto.
      * Si es así, se devuelve la entidad existente.
      * En caso contrario, se crea una entidad nueva, se configura el seguimiento de cambios y se devuelve la entidad nueva.
   2. Si se trata de una consulta que no es de seguimiento, siempre se crea y devuelve una entidad nueva.

## <a name="when-queries-are-executed"></a>Cuándo se ejecutan las consultas

Cuando llama a los operadores LINQ, simplemente crea una representación de la consulta en memoria. La consulta solo se envía a la base de datos cuando se usan los resultados.

Las operaciones más comunes que generan que la consulta se envíe a la base de datos son:

* La iteración de los resultados en un bucle `for`
* El uso de operadores como `ToList`, `ToArray`, `Single` y `Count` o las sobrecargas asincrónicas equivalentes

> [!WARNING]  
> **Valide siempre la entrada del usuario:** aunque EF Core protege contra los ataques por inyección de código SQL con el uso de parámetros y el escape de cadenas literales en consultas, no valida las entradas. Se debe realizar una validación apropiada, según los requisitos de la aplicación, antes de que los valores de los orígenes que no son de confianza se usen en consultas LINQ, se asignen a las propiedades de una entidad o se pasen a otras API de EF Core. Esto incluye cualquier intervención del usuario que se use para construir consultas de manera dinámica. Incluso al usar LINQ, si acepta la intervención del usuario para crear expresiones, necesita garantizar que solo se pueden construir las expresiones previstas.
