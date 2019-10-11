---
title: Actualización a Entity Framework 6-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 4395a9c117a6cf38e7fc08f11ee689d6fffa6fed
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182098"
---
# <a name="upgrading-to-entity-framework-6"></a>Actualización a Entity Framework 6

En versiones anteriores de EF, el código se dividió entre las bibliotecas principales (principalmente System. Data. Entity. dll) que se distribuyeron como parte de las bibliotecas de .NET Framework y fuera de banda (OOB) (principalmente EntityFramework. dll) incluidas en un paquete NuGet. EF6 toma el código de las bibliotecas principales y lo incorpora a las bibliotecas de OOB. Esto era necesario para permitir que EF se convirtió en código abierto y para que pueda evolucionar a un ritmo diferente del .NET Framework. La consecuencia de esto es que las aplicaciones deben volver a generarse con los tipos que se han descargado.

Esto debe ser sencillo para las aplicaciones que hacen uso de DbContext, tal y como se distribuyen en EF 4,1 y versiones posteriores. Se requiere un poco más de trabajo para las aplicaciones que hacen uso de ObjectContext pero que todavía no es difícil de hacer.

Esta es una lista de comprobación de las cosas que debe hacer para actualizar una aplicación existente a EF6.

## <a name="1-install-the-ef6-nuget-package"></a>1. Instalación del paquete NuGet de EF6

Debe actualizar al nuevo tiempo de ejecución de Entity Framework 6.

1. Haga clic con el botón derecho en el proyecto y seleccione **administrar paquetes NuGet...**  
2. En la pestaña **en línea** , seleccione **EntityFramework** y haga clic en **instalar** .  
   > [!NOTE]
   > Si se instaló una versión anterior del paquete NuGet EntityFramework, se actualizará a EF6.

Como alternativa, puede ejecutar el siguiente comando desde la consola del administrador de paquetes:

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. Asegurarse de que se quitan las referencias de ensamblado a System. Data. Entity. dll

La instalación del paquete NuGet de EF6 debe quitar automáticamente del proyecto todas las referencias a System. Data. Entity.

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. Intercambie los modelos de EF Designer (EDMX) para usar la generación de código EF 6. x

Si tiene modelos creados con el diseñador de EF, deberá actualizar las plantillas de generación de código para generar código compatible con EF6.

> [!NOTE]
> Actualmente solo hay plantillas de generador de DbContext de EF 6. x disponibles para Visual Studio 2012 y 2013.

1. Elimine las plantillas de generación de código existentes. Normalmente, estos archivos se denominarán **@no__t -1edmx_file_name\>.tt** y **\<edmx_file_name @ no__t-5. Context.tt** y estar anidados en el archivo edmx en explorador de soluciones. Puede seleccionar las plantillas en Explorador de soluciones y presionar la tecla **Supr** para eliminarlas.  
   > [!NOTE]
   > En los proyectos de sitio web, las plantillas no se anidarán en el archivo edmx, sino que se enumeran en Explorador de soluciones.  

   > [!NOTE]
   > En los proyectos de VB.NET, deberá habilitar "Mostrar todos los archivos" para poder ver los archivos de plantilla anidados.
2. Agregue la plantilla de generación de código EF 6. x adecuada. Abra el modelo en EF Designer, haga clic con el botón derecho en la superficie de diseño y seleccione **Agregar elemento de generación de código..** .
    - Si usa la API DbContext (recomendado), el **generador de dbcontext de EF 6. x** estará disponible en la pestaña **datos** .  
      > [!NOTE]
      > Si usa Visual Studio 2012, tendrá que instalar las herramientas de EF 6 para tener esta plantilla. Consulte [obtener Entity Framework](~/ef6/fundamentals/install.md) para obtener más información.  

    - Si usa la API de ObjectContext, tendrá que seleccionar la pestaña **en línea** y buscar el generador de **EntityObject EF 6. x**.  
3. Si ha aplicado cualquier personalización a las plantillas de generación de código, deberá volver a aplicarlas a las plantillas actualizadas.

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. Actualizar los espacios de nombres de los tipos de EF principales utilizados

Los espacios de nombres de los tipos DbContext y Code First no han cambiado. Esto significa que para muchas aplicaciones que usan EF 4,1 o posterior, no es necesario cambiar nada.

Los tipos como ObjectContext que anteriormente se encontraban en System. Data. Entity. dll se han migrado a nuevos espacios de nombres. Esto significa que es posible que tenga que actualizar las directivas *using* o *Import* para compilar en EF6.

La regla general para los cambios en el espacio de nombres es que cualquier tipo de System. Data. * se mueve a System. Data. Entity. Core. *. En otras palabras, simplemente inserte **Entity. Core.** después de System. Data. Por ejemplo:

- System. Data. EntityException = > System. Data. **Entity. Core**. EntityException  
- System. Data. Objects. ObjectContext = > System. Data. **Entity. Core**. Objects. ObjectContext  
- System. Data. Objects. Classes. RelationshipManager = > System. Data. **Entity. Core**. Objects. Classes. RelationshipManager  

Estos tipos se encuentran en los espacios de nombres *básicos* porque no se usan directamente para la mayoría de las aplicaciones basadas en DbContext. Algunos tipos que formaban parte de System. Data. Entity. dll se siguen usando normalmente y directamente para aplicaciones basadas en DbContext, por lo que no se han pasado a los espacios de nombres *principales* . Estos son:

- System. Data. EntityState = > System. Data. **Entidad**. EntityState  
- System. Data. Objects. Classes. EdmFunctionAttribute = > System. Data. **Entity. DbFunctionAttribute**  
  > [!NOTE]
  > Se ha cambiado el nombre de esta clase; todavía existe una clase con el nombre anterior y funciona, pero ahora está marcada como obsoleta.  
- System. Data. Objects. EntityFunctions = > System. Data. **Entity. DbFunctions**  
  > [!NOTE]
  > Se ha cambiado el nombre de esta clase; todavía existe una clase con el nombre antiguo y funciona, pero ahora está marcada como obsoleta).  
- Las clases espaciales (por ejemplo, DbGeography, DbGeometry) se han pasado de System. Data. Spatial = > System. Data. **Entidad**. PDF

> [!NOTE]
> Algunos tipos del espacio de nombres System. Data están en System. Data. dll, que no es un ensamblado EF. Estos tipos no se han cambiado y, por tanto, sus espacios de nombres permanecen inalterados.
