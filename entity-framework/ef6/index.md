---
title: Información general de Entity Framework 6 - EF6
description: Información general de Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/index
ms.openlocfilehash: aff7aee51c4e12b278f7cab8b5122ee0edbbac0f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072127"
---
# <a name="entity-framework-6"></a>Entity Framework 6
Entity Framework 6 (EF6) es un asignador relacional de objetos (O/RM) probado para .NET con muchos años de desarrollo de características y estabilización.

Como O/RM, EF6 reduce la discordancia de impedancia entre los mundos relacionales y orientados a objetos, lo que permite a los desarrolladores escribir aplicaciones que interactúan con datos almacenados en bases de datos relacionales con objetos .NET fuertemente tipados que representan el dominio de la aplicación, y eliminar la necesidad de una gran parte del código de "mecánica" de acceso de datos que normalmente deben escribir.

EF6 implementa muchas características de O/RM populares:
- Asignación de clases de entidad [POCO](xref:ef6/resources/glossary#poco) que no dependen de ningún tipo de EF
- Seguimiento de cambios automático
- Resolución de identidad y unidad de trabajo
- Carga diligente, diferida y explícita
- Traducción de consultas fuertemente tipadas con [LINQ (Language Integrated Query)](https://aka.ms/AA6hsvu)
- Capacidades de asignación enriquecidas que incluyen compatibilidad con:
  - Relaciones de uno a uno, de uno a varios y entre varios
  - Herencia (tabla por jerarquía, tabla por tipo y tabla por clase concreta)
  - Tipos complejos
  - Procedimientos almacenados
- Un diseñador visual para crear modelos de entidad.
- Una experiencia "Code First" para crear modelos de entidad mediante la escritura de código.
- Los modelos pueden generarse a partir de bases de datos existentes y luego editarse manualmente, o bien se pueden crear desde cero y luego usarse para generar nuevas bases de datos.
- Integración con modelos de aplicación de .NET Framework, incluido ASP.NET, y mediante enlace de datos, con WPF y WinForms.
- Conectividad de base de datos basada en ADO.NET y varios [proveedores](xref:ef6/fundamentals/providers/index) disponibles para conectarse a SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.

## <a name="should-i-use-ef6-or-ef-core"></a>¿Debo usar EF6 o EF Core?

EF Core es una versión más moderna, ligera y extensible de Entity Framework que tiene capacidades y ventajas muy similares a EF6.
EF Core es una reescritura completa y contiene muchas características nuevas que no están disponibles en EF6, aunque todavía carece de algunas de las funcionalidades más avanzadas de asignación de EF6.
Considere el uso de EF Core en las aplicaciones nuevas si el conjunto de características se ajusta a los requisitos.
En [Comparar EF Core y EF6](xref:efcore-and-ef6/index) se examina el proceso de elección más detalladamente.

## <a name="get-started"></a>[Introducción](xref:ef6/get-started)

Agregue el paquete NuGet de EntityFramework al proyecto o instale [Entity Framework Tools para Visual Studio](https://aka.ms/AA6i8c5). Luego, vea vídeos, lea tutoriales y consulte documentación avanzada, que le ayudarán a sacar el máximo partido de EF6.

## <a name="past-entity-framework-versions"></a>Versiones anteriores de Entity Framework

Esta es la documentación de la versión más reciente de Entity Framework 6, aunque la mayor parte también se aplica a las versiones anteriores.
Vea [Novedades](xref:ef6/what-is-new/index) y [Versiones anteriores](xref:ef6/what-is-new/past-releases) para obtener una lista completa de las versiones de EF y las características incluidas.
