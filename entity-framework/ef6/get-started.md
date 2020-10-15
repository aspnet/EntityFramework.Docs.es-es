---
title: 'Introducción a Entity Framework 6: EF6'
description: Introducción a Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: b518b8ec70045066ffce6ac2a32136df97e14e99
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062794"
---
# <a name="get-started-with-entity-framework-6"></a>Introducción a Entity Framework 6

En esta guía se recopilan vínculos a artículos de documentación, tutoriales y vídeos seleccionados que le pueden ayudar a empezar rápidamente.

## <a name="fundamentals"></a>Aspectos básicos

* [Obtener Entity Framework](xref:ef6/fundamentals/install)

  Aquí obtendrá información sobre cómo agregar Entity Framework a sus aplicaciones y, si quiere usar EF Designer, asegúrese de que lo instala en Visual Studio.

* [Creación de un modelo: Code First, EF Designer y flujos de trabajo EF](xref:ef6/modeling/index)

  ¿Prefiere especificar un código de escritura de modelos de EF o líneas y cuadros de dibujo?
¿Va a usar EF para asignar objetos a una base de datos existente o quiere que cree una base de datos específica para los objetos?
Aquí podrá obtener información sobre dos enfoques diferentes para usar EF6: EF Designer y Code First.
Asegúrese de seguir el debate y vea el vídeo sobre las diferencias.

* [Trabajar con DbContext](xref:ef6/fundamentals/working-with-dbcontext)

  DbContext es el primer tipo de EF y el más importante. Necesita conocerlo para saber cómo usarlo. Actúa como plataforma para consultas de bases de datos y mantiene un seguimiento de los cambios que hace a objetos, de modo que puedan persistir en la base de datos.

* [Formular una pregunta](xref:ef6/resources/get-help)

  Descubra cómo obtener ayuda de expertos y contribuya con sus respuestas a la comunidad.

* [Colaboracion](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 usa un modelo de desarrollo abierto. Descubra cómo puede ayudar a asegurarse de que EF sea aún mejor en nuestro repositorio de GitHub.

## <a name="code-first-resources"></a>Recursos de Code First

  - [Code First en un flujo de trabajo de base de datos existente](xref:ef6/modeling/code-first/workflows/existing-database)
  - [Code First en un nuevo flujo de trabajo de base de datos](xref:ef6/modeling/code-first/workflows/new-database)
  - [Asignación de enumeraciones con Code First](xref:ef6/modeling/code-first/data-types/enums)
  - [Asignación de tipos espaciales con Code First](xref:ef6/modeling/code-first/data-types/spatial)
  - [Creación de convenciones personalizadas e Code First](xref:ef6/modeling/code-first/conventions/custom)
  - [Usar la configuración de Fluent de Code First con Visual Basic](xref:ef6/modeling/code-first/fluent/vb)
  - [Migraciones de Code First](xref:ef6/modeling/code-first/migrations/index)
  - [Code First Migrations in Team Environments](xref:ef6/modeling/code-first/migrations/teams) (Migraciones de Code First en entornos de equipo)
  - [Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (Migraciones automáticas de Code First, ya no se recomienda)

## <a name="ef-designer-resources"></a>Recursos de EF Designer
  - [Flujo de trabajo de Database First](xref:ef6/modeling/designer/workflows/database-first)
  - [Flujo de trabajo de Model First](xref:ef6/modeling/designer/workflows/model-first)
  - [Asignación de enumeraciones](xref:ef6/modeling/designer/data-types/enums)
  - [Asignación de tipos espaciales](xref:ef6/modeling/designer/data-types/spatial)
  - [Asignación de herencia de tabla por jerarquía](xref:ef6/modeling/designer/inheritance/tph)
  - [Asignación de herencia de tabla por tipo](xref:ef6/modeling/designer/inheritance/tpt)
  - [Asignación de procedimientos almacenados para actualizaciones](xref:ef6/modeling/designer/stored-procedures/cud)
  - [Asignación de procedimientos almacenados para consultas](xref:ef6/modeling/designer/stored-procedures/query)
  - [División de entidades](xref:ef6/modeling/designer/entity-splitting)
  - [División de tablas](xref:ef6/modeling/designer/table-splitting)
  - [Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Definición de consultas, avanzado)
  - [Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Funciones con valores de tabla, avanzado)

## <a name="other-resources"></a>Otros recursos
  - [Async Query and Save](xref:ef6/fundamentals/async) (Guardado y consultas asincrónicas)
  - [Databinding with WinForms](xref:ef6/fundamentals/databinding/winforms) (Enlace de datos con WinForms)
  - [Databinding with WPF](xref:ef6/fundamentals/databinding/wpf) (Enlace de datos con WPF)
  - [Escenarios desconectados con entidades de autoseguimiento](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (ya no se recomienda)
