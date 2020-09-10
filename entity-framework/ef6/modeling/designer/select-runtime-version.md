---
title: 'Selección de Entity Framework versión en tiempo de ejecución para los modelos de EF Designer: EF6'
description: Selección de Entity Framework versión en tiempo de ejecución para los modelos EF Designer en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 77830aaa3c7365e05e867d4ced88bdb8a11e7d6b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620394"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>Selección de Entity Framework versión en tiempo de ejecución para los modelos EF Designer
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

A partir de EF6, se ha agregado la siguiente pantalla a EF Designer para que pueda seleccionar la versión del tiempo de ejecución al que desea dirigirse al crear un modelo. La pantalla aparecerá cuando la versión más reciente de Entity Framework no esté ya instalada en el proyecto. Si ya está instalada la versión más reciente, se usará de forma predeterminada.

![Seleccionar versión de tiempo de ejecución](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a>Destino de EF6. x

Puede elegir EF6 en la pantalla "elegir su versión" para agregar el tiempo de ejecución de EF6 al proyecto. Una vez que haya agregado EF6, dejará de ver esta pantalla en el proyecto actual.

EF6 se deshabilitará si ya tiene instalada una versión anterior de EF (ya que no puede tener como destino varias versiones del tiempo de ejecución del mismo proyecto). Si la opción EF6 no está habilitada aquí, siga estos pasos para actualizar el proyecto a EF6:

1.  Haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar paquetes NuGet...**
2.  Seleccionar **actualizaciones**
3.  Seleccione **EntityFramework** (Asegúrese de que se va a actualizar a la versión que quiera)
4.  Haga clic en **Actualizar**

 

## <a name="targeting-ef5x"></a>Destino de EF5. x

Puede elegir EF5 en la pantalla "elegir su versión" para agregar el tiempo de ejecución de EF5 al proyecto. Una vez que haya agregado EF5, seguirá viendo la pantalla con la opción EF6 deshabilitada.

Si ya tiene instalada una versión de EF4. x del Runtime, verá que la versión de EF aparece en la pantalla en lugar de EF5. En esta situación, puede actualizar a EF5 siguiendo estos pasos:

1.  Seleccione **herramientas- &gt; Administrador de paquetes de la biblioteca- &gt; consola del administrador de paquetes**
2.  Ejecute **Install-Package EntityFramework-version 5.0.0**

 

## <a name="targeting-ef4x"></a>Destino de EF4. x

Puede instalar el tiempo de ejecución de EF4. x en el proyecto mediante los pasos siguientes:

1.  Seleccione **herramientas- &gt; Administrador de paquetes de la biblioteca- &gt; consola del administrador de paquetes**
2.  Ejecute **Install-Package EntityFramework-version 4.3.0**
