---
title: Revertir a ObjectContext en Entity Framework Designer-EF6
description: Revertir a ObjectContext en Entity Framework Designer en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/legacy-objectcontext
ms.openlocfilehash: 563c1defd12a72836a158d9c2398202c421dd6f3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064944"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Revertir a ObjectContext en Entity Framework Designer
Con la versión anterior de Entity Framework un modelo creado con EF Designer generaría un contexto derivado de ObjectContext y clases de entidad derivadas de EntityObject.

A partir de EF 4.1, se recomienda cambiar a una plantilla de generación de código que genera un contexto que se deriva de las clases de entidad DbContext y POCO.

En Visual Studio 2012, se obtiene el código DbContext generado de forma predeterminada para todos los nuevos modelos creados con el diseñador de EF. Los modelos existentes seguirán generando código basado en ObjectContext a menos que decida cambiar al generador de código basado en DbContext.

## <a name="reverting-back-to-objectcontext-code-generation"></a>Revertir a la generación de código de ObjectContext

### <a name="1-disable-dbcontext-code-generation"></a>1. deshabilitar la generación de código DbContext

La generación de las clases DbContext y POCO derivadas se controla mediante dos archivos. TT en el proyecto. Si expande el archivo. edmx en el explorador de soluciones, verá estos archivos. Elimine ambos archivos del proyecto.

![Archivos de generación de código](~/ef6/media/codegenfiles.png)

Si usa VB.NET, debe seleccionar el botón **Mostrar todos los archivos** para ver los archivos anidados.

![Mostrar todos los archivos](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. Re-Enable la generación de código de ObjectContext

Abra el modelo en el diseñador de EF, haga clic con el botón derecho en una sección en blanco de la superficie de diseño y seleccione **propiedades**.

En el ventana Propiedades cambie la **estrategia de generación de código** de **ninguno** a **predeterminado**.

![Estrategia de generación de código](~/ef6/media/codegenstrategy.png)
