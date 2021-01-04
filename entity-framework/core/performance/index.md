---
title: 'Introducción al rendimiento: EF Core'
description: Guía de rendimiento para usar Entity Framework Core de forma eficaz
author: roji
ms.date: 12/1/2020
uid: core/miscellaneous/performance/index
ms.openlocfilehash: 14400d81ea3c93e2ebf40e8e585a457abf31478f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657691"
---
# <a name="introduction-to-performance"></a>Introducción al rendimiento

El rendimiento de la base de datos es un tema amplio y complejo que abarca toda una pila de componentes: la base de datos, la red, el controlador de base de datos y los niveles de acceso a datos, como EF Core. Aunque los niveles generales y los O/RM como EF Core simplifican considerablemente el desarrollo de aplicaciones y mejoran la facilidad de mantenimiento, en ocasiones pueden ser opacos y ocultar detalles internos críticos para el rendimiento, como el SQL que se ejecuta. En esta sección se intenta proporcionar información general sobre cómo conseguir un buen rendimiento con EF Core y cómo evitar errores comunes que pueden degradar el rendimiento de la aplicación.

## <a name="identify-bottlenecks-and-measure-measure-measure"></a>Identificación de cuellos de botella y medidas continuadas

Como sucede siempre con el rendimiento, es importante no apresurarse en la optimización sin contar con datos que muestren un problema; como el gran Donald Knuth afirmó, "La optimización prematura es la raíz de todos los males". En la sección de [diagnóstico del rendimiento](xref:core/performance/performance-diagnosis) se describen varias maneras de entender dónde dedica tiempo la aplicación en la lógica de base de datos y cómo identificar áreas problemáticas concretas. Una vez que se ha identificado una consulta lenta, se pueden barajar las soluciones: ¿falta un índice en la base de datos? ¿Se deben probar otros modelos de consulta?

Siempre debe someter a un banco de pruebas el código y las posibles alternativas personalmente: la sección de diagnóstico del rendimiento contiene un banco de pruebas de ejemplo con BenchmarkDotNet, que puede usar como plantilla para pruebas comparativas propias. No suponga que los bancos de pruebas públicos y generales se aplican tal cual a cada caso de uso concreto; una gran variedad de factores, como la latencia de la base de datos, la complejidad de las consultas y las cantidades de datos reales de las tablas, pueden tener un impacto profundo en la solución más adecuada. Por ejemplo, muchos bancos de pruebas públicos se usan bajo condiciones de red idóneas, donde la latencia para la base de datos es casi cero y con consultas extremadamente ligeras que apenas requieren procesamiento (o E/S de disco) en la base de datos. Aunque son valiosos para comparar las sobrecargas en tiempo de ejecución de los diferentes niveles de acceso a datos, las diferencias que revelan suelen ser insignificantes en una aplicación real, donde la base de datos realiza el trabajo real y la latencia en la base de datos es un factor de rendimiento importante.

## <a name="aspects-of-data-access-performance"></a>Aspectos del rendimiento de acceso a datos

El rendimiento general del acceso a datos se puede dividir en las siguientes categorías generales:

* **Rendimiento de base de datos puro**. Con las bases de datos relacionales, EF traduce las consultas LINQ de la aplicación en las instrucciones SQL que ejecuta la base de datos; estas instrucciones SQL se pueden ejecutar de forma más o menos eficaz. El índice adecuado en el lugar adecuado puede marcar considerablemente las diferencias para el rendimiento de SQL, o bien volver a escribir la consulta LINQ puede hacer que EF genere una consulta SQL mejor.
* **Transferencia de datos entre redes**. Como sucede con cualquier sistema de redes, es importante limitar la cantidad de datos que se transmiten por la conexión. Esto implica asegurarse de que solo se envían y cargan los datos que se van a necesitar, pero que también se evita el efecto denominado "explosión cartesiana" al cargar entidades relacionadas.
* **Recorridos de ida y vuelta de red**. Más allá de la cantidad de datos que se transmiten, los recorridos de ida y vuelta de red, ya que el tiempo que se tarda en ejecutar una consulta en la base de datos puede verse reducido por el tiempo de desplazamiento de los paquetes entre la aplicación y la base de datos. La sobrecarga de los recorridos de ida y vuelta depende en gran medida del entorno; cuanto más lejos esté el servidor de base de datos, mayor será la latencia y más costoso cada recorrido de ida y vuelta. Con la llegada de la nube, las aplicaciones se encuentran cada vez más lejos de la base de datos y las más activas que realizan demasiados recorridos de ida y vuelta sufren un rendimiento degradado. Por tanto, es importante comprender exactamente cuándo la aplicación se pone en contacto con la base de datos, cuántos recorridos de ida y vuelta realiza, y si ese número se puede minimizar.
* **Sobrecarga de tiempo de ejecución de EF**. Finalmente, EF agrega una sobrecarga en tiempo de ejecución a las operaciones de base de datos: EF debe compilar las consultas desde LINQ to SQL (aunque normalmente solo se debe hacer una vez), el seguimiento de los cambios agrega cierta sobrecarga (pero se puede deshabilitar), etc. En la práctica, es probable que la sobrecarga de EF para las aplicaciones reales sea insignificante en la mayoría de los casos, ya que el tiempo de ejecución de la consulta en la base de datos y la latencia de red dominan el tiempo total; pero es importante comprender cuáles son las opciones y cómo evitar algunos problemas.

## <a name="know-whats-happening-under-the-hood"></a>Saber lo que ocurre en segundo plano

EF permite a los desarrolladores concentrarse en la lógica empresarial mediante la generación de SQL, la materialización de resultados y la realización de otras tareas. Como sucede con cualquier nivel o abstracción, también tiende a ocultar lo que sucede en segundo plano, como las consultas SQL reales que se ejecutan. El rendimiento no es necesariamente un aspecto fundamental de todas las aplicaciones, sino que, en las aplicaciones en las que lo es, es imprescindible que el desarrollador comprenda lo que hace EF: inspeccionar las consultas SQL salientes, seguir los recorridos de ida y vuelta para asegurarse de que el problema N+1 no se produzca, etc.

## <a name="cache-outside-the-database"></a>Almacenamiento en caché fuera de la base de datos

Por último, la manera más eficaz de interactuar con una base de datos es no interactuar con ella. Es decir, si el acceso a la base de datos aparece como un cuello de botella de rendimiento en la aplicación, puede merecer la pena almacenar en caché determinados resultados fuera de la base de datos, para minimizar las solicitudes. Aunque el almacenamiento en caché aumenta la complejidad, es una parte fundamental de cualquier aplicación escalable: mientras que la capa de aplicación se puede escalar fácilmente mediante la adición de más servidores para controlar el aumento de la carga, el escalado del nivel de base de datos suele ser mucho más complicado.
