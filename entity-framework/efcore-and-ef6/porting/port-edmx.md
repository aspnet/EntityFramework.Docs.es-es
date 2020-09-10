---
title: 'Portabilidad de EF6 a EF Core: portabilidad de un modelo basado en EDMX - EF'
description: Información específica acerca de cómo migrar una aplicación de modelo basada en EDMX de Entity Framework 6 a Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 3767b1dc083ec886115cea9b0750101fb49ad84c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619602"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>Portabilidad de un modelo basado en EDMX de EF6 a EF Core

EF Core no admite el formato de archivo EDMX para los modelos. La mejor opción para realizar la portabilidad de estos modelos consiste en generar un modelo nuevo basado en código a partir de la base de datos de la aplicación.

## <a name="install-ef-core-nuget-packages"></a>Instalación de los paquetes NuGet de EF Core

Instale el paquete NuGet `Microsoft.EntityFrameworkCore.Tools`.

## <a name="regenerate-the-model"></a>Regeneración del modelo

Ahora puede usar la funcionalidad de ingeniería inversa para crear un modelo basado en la base de datos existente.

Ejecute el comando siguiente en la consola del Administrador de paquetes NuGet (Herramientas –> Administrador de paquetes NuGet –> Consola del Administrador de paquetes). Vea [Consola del Administrador de paquetes (Visual Studio)](xref:core/miscellaneous/cli/powershell) para conocer las opciones de comando para aplicar scaffolding a un subconjunto de tablas, etc.

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

Por ejemplo, este es el comando para aplicar scaffolding a un modelo a partir de la base de datos Blogging en la instancia de LocalDB de SQL Server.

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>Eliminación del modelo de EF6

Ahora se quitará el modelo de EF6 de la aplicación.

No hay problema por dejar el paquete NuGet de EF6 (EntityFramework) instalado, ya que EF Core y EF6 se pueden usar en paralelo en la misma aplicación. Sin embargo, si no va a usar EF6 en ninguna de las áreas de la aplicación, la desinstalación del paquete le ayudará a dar errores de compilación en fragmentos de código que requieren atención.

## <a name="update-your-code"></a>Actualización del código

En este punto, es cuestión de solucionar los errores de compilación y de revisar el código para ver si los cambios de comportamiento entre EF6 y EF Core le afectarán.

## <a name="test-the-port"></a>Prueba del puerto

El hecho de que la aplicación se compile no significa que se haya trasladado correctamente a EF Core. Tendrá que probar todas las áreas de la aplicación para asegurarse de que ninguno de los cambios de comportamiento afecte de forma negativa a la aplicación.
