---
title: Casos prácticos de Entity Framework-EF6
description: Casos prácticos de Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: cd5d3ae3-717d-4095-a2ef-0e8fd72b1a2f
ms.openlocfilehash: f6cabe34cfbdc5f15758678f458b2c0898198137
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620242"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Casos prácticos de Microsoft para Entity Framework
En los casos prácticos de esta página se resaltan algunos proyectos de producción reales que han empleado Entity Framework.
> [!NOTE]
> En el sitio web de Microsoft ya no están disponibles las versiones detalladas de estos casos de estudio. Por lo tanto, se han quitado los vínculos.

## <a name="epicor"></a>Epopeya
La epopeya es una empresa de software global de gran tamaño (con más de 400 desarrolladores) que desarrolla soluciones de planeamiento de recursos empresariales (ERP) para empresas de más de 150 países.
Su producto insignia, Epicr 9, se basa en una arquitectura orientada a servicios (SOA) mediante el .NET Framework.
Se enfrenta a numerosas solicitudes de clientes para proporcionar compatibilidad con Language Integrated Query (LINQ) y también para reducir la carga en sus servidores de SQL back-end, el equipo decidió actualizar a Visual Studio 2010 y el .NET Framework 4,0.
Con el Entity Framework 4,0, pudieron alcanzar estos objetivos y simplificar enormemente el desarrollo y el mantenimiento.
En concreto, la compatibilidad con T4 enriquecida del Entity Framework les permitió tomar el control completo de su código generado y compilar automáticamente características de ahorro de rendimiento como las consultas precompiladas y el almacenamiento en caché.

> "Hemos realizado algunas pruebas de rendimiento recientemente con el código existente y pudimos reducir las solicitudes a SQL Server por un 90 por ciento.
Esto se debe a ADO.NET Entity Framework 4. " – Erik Johnson, Vicepresidente, investigación del producto  

## <a name="veracity-solutions"></a>Soluciones de veracidad
Habiendo adquirido un sistema de software de planeación de eventos que iba a ser difícil de mantener y extenderse a través de las soluciones de veracidad a largo plazo que usaba Visual Studio 2010 para volver a escribirlo como una aplicación de Internet eficaz y fácil de usar basada en Silverlight 4.
Con .NET RIA Services, podían crear rápidamente una capa de servicio sobre el Entity Framework que evitaba la duplicación de código y permitía la validación común y la lógica de autenticación en los niveles.  

> "Se vendió en el Entity Framework la primera vez que se presentó y el Entity Framework 4 ha demostrado ser aún mejor.
Se han mejorado las herramientas y es más fácil manipular los archivos. edmx que definen el modelo conceptual, el modelo de almacenamiento y la asignación entre esos modelos... Con el Entity Framework, puedo conseguir que el nivel de acceso a datos funcione en un día y compilarlo a medida que avance.
La Entity Framework es nuestra capa de acceso a datos de facto; No sé por qué nadie lo usaría ". – Joe McBride, Desarrollador Senior

## <a name="nec-display-solutions-of-america"></a>NEC muestra soluciones de América
NEC deseaba entrar en el mercado de publicidad basada en el lugar digital con una solución para beneficiar a los anunciantes y propietarios de la red, así como para aumentar sus ingresos.
Para ello, inició un par de aplicaciones web que automatizan los procesos manuales necesarios en una campaña de ad tradicional.
Los sitios se crearon con ASP.NET, Silverlight 3, AJAX y WCF, junto con los Entity Framework en la capa de acceso a datos para comunicarse con SQL Server 2008.

> "Con SQL Server, pensamos que podríamos obtener el rendimiento que necesitábamos para servir a los anunciantes y redes con información en tiempo real y la confiabilidad para garantizar que la información de nuestras aplicaciones críticas siempre estuviera disponible"-Mike Corcoran, Director de ti

## <a name="darwin-dimensions"></a>Dimensiones de Darwin
Gracias a una amplia gama de tecnologías de Microsoft, el equipo de Darwin ha establecido la creación de un portal de Avatar en línea para que los consumidores puedan utilizarla con el fin de crear atractivas avatares reales para su uso en juegos, animaciones y páginas de redes sociales.
Con las ventajas de productividad del Entity Framework y la extracción de componentes como Windows Workflow Foundation (WF) y Windows Server AppFabric (una memoria caché de aplicaciones en memoria altamente escalable), el equipo pudo ofrecer un producto sorprendente en un 35% menos tiempo de desarrollo.
A pesar de que los miembros del equipo se dividen en varios países, el equipo sigue un proceso de desarrollo ágil con versiones semanales.

 > "Intentamos no crear tecnología para la tecnología. Como inicio, es fundamental que se aproveche la tecnología que ahorra tiempo y dinero.
 .NET era la elección para el desarrollo rápido y rentable ". – Zachary Olsen, arquitecto  

## <a name="silverware"></a>Silverware
Con más de 15 años de experiencia en el desarrollo de soluciones de punto de venta (POS) para grupos de restaurante pequeños y medianas, el equipo de desarrollo de silverware se ha establecido para mejorar su producto con características de nivel empresarial más para atraer cadenas de restaurante más grandes.
Con la versión más reciente de las herramientas de desarrollo de Microsoft, podían crear la nueva solución cuatro veces más rápido que antes.
Las nuevas características clave como LINQ y el Entity Framework facilitan el traslado de Crystal Reports a SQL Server 2008 y SQL Server Reporting Services (SSRS) para sus necesidades de informes y almacenamiento de datos.

> "La administración eficaz de los datos es fundamental para el éxito de SilverWare, y por eso se decidió adoptar SQL Reporting". -Nicholas Romanidis, Director de ingeniería de ti/software
