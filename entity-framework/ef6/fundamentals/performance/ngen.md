---
title: Mejorar el rendimiento de inicio con NGen-EF6
description: Mejorar el rendimiento de inicio con NGen en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: a58ffc1a4da1cdb0ae12366ab1ec0139141b3a7f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065425"
---
# <a name="improving-startup-performance-with-ngen"></a>Mejorar el rendimiento de inicio con NGen
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

El .NET Framework admite la generación de imágenes nativas para aplicaciones y bibliotecas administradas como una manera de ayudar a las aplicaciones a iniciarse más rápido y, en algunos casos, usar menos memoria. Las imágenes nativas se crean mediante la conversión de ensamblados de código administrado en archivos que contienen instrucciones máquina nativas antes de que se ejecute la aplicación, lo que evita que el compilador JIT de .NET (Just-in-Time) tenga que generar las instrucciones nativas en tiempo de ejecución de la aplicación.  

Antes de la versión 6, las bibliotecas principales del tiempo de ejecución de EF formaban parte del .NET Framework y las imágenes nativas se generaban automáticamente para ellas. A partir de la versión 6, todo el tiempo de ejecución de EF se ha combinado en el paquete NuGet de EntityFramework. Ahora, las imágenes nativas deben generarse con la herramienta de línea de comandos NGen.exe para obtener resultados similares.  

Las observaciones empíricas muestran que las imágenes nativas de los ensamblados en tiempo de ejecución de EF pueden cortar entre 1 y 3 segundos de tiempo de inicio de la aplicación.  

## <a name="how-to-use-ngenexe"></a>Cómo usar NGen.exe  

La función más básica de la herramienta NGen.exe es "instalar" (es decir, crear y conservar en disco) imágenes nativas para un ensamblado y todas sus dependencias directas. Aquí se muestra cómo lograr esto:  

1. Abra una ventana de símbolo del sistema como administrador.
2. Cambie el directorio de trabajo actual a la ubicación de los ensamblados para los que desea generar imágenes nativas:

   ``` console
   cd <*Assemblies location*>  
   ```

3. En función del sistema operativo y de la configuración de la aplicación, es posible que tenga que generar imágenes nativas para la arquitectura de 32 bits, la arquitectura de 64 bits o para ambas.

   Para la ejecución de 32 bits:

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   Para la ejecución de 64 bits:
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> La generación de imágenes nativas para la arquitectura equivocada es un error muy común. En caso de duda, simplemente puede generar imágenes nativas para todas las arquitecturas que se aplican al sistema operativo instalado en la máquina.  

NGen.exe también admite otras funciones, como desinstalar y mostrar las imágenes nativas instaladas, poner en cola la generación de varias imágenes, etc. Para obtener más detalles sobre el uso, lea la [ documentación deNGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx).  

## <a name="when-to-use-ngenexe"></a>Cuándo usar NGen.exe  

Cuando se trata de decidir en qué ensamblados se van a generar imágenes nativas en una aplicación basada en EF versión 6 o versiones posteriores, debe tener en cuenta las siguientes opciones:  

- **El ensamblado de tiempo de ejecución de EF principal, EntityFramework.dll**: una aplicación basada en EF típica ejecuta una cantidad significativa de código de este ensamblado en el inicio o en el primer acceso a la base de datos. Por lo tanto, la creación de imágenes nativas de este ensamblado producirá las mayores mejoras en el rendimiento de inicio.  
- **Cualquier ensamblado de proveedor EF usado por la aplicación**: el tiempo de inicio también puede beneficiarse ligeramente de la generación de imágenes nativas. Por ejemplo, si la aplicación utiliza el proveedor de EF para SQL Server querrá generar una imagen nativa para EntityFramework.SqlServer.dll.  
- **Los ensamblados de la aplicación y otras dependencias**: en la [ documentaciónNGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx) se tratan los criterios generales para elegir en qué ensamblados se van a generar imágenes nativas y el impacto de las imágenes nativas en la seguridad, opciones avanzadas como "enlace fuerte", escenarios como el uso de imágenes nativas en escenarios de depuración y generación de perfiles, etc.  

> [!TIP]
> Asegúrese de medir cuidadosamente el impacto del uso de imágenes nativas en el rendimiento de inicio y el rendimiento general de la aplicación y compararlas con los requisitos reales. Mientras que las imágenes nativas suelen ayudar a mejorar el rendimiento de inicio y, en algunos casos, reducir el uso de memoria, no todos los escenarios se beneficiarán de forma equitativa. Por ejemplo, en la ejecución de estado estable (es decir, una vez que todos los métodos que se usan en la aplicación se han invocado al menos una vez), el código generado por el compilador JIT puede producir de hecho un rendimiento ligeramente mejor que las imágenes nativas.  

## <a name="using-ngenexe-in-a-development-machine"></a>Usar NGen.exe en una máquina de desarrollo  

Durante el desarrollo, el compilador JIT de .NET ofrecerá el mejor equilibrio general para el código que cambia con frecuencia. La generación de imágenes nativas para las dependencias compiladas, como los ensamblados en tiempo de ejecución de EF, puede ayudar a acelerar el desarrollo y las pruebas reduciendo unos pocos segundos al principio de cada ejecución.  

Un buen lugar para encontrar los ensamblados en tiempo de ejecución de EF es la ubicación del paquete NuGet para la solución. Por ejemplo, para una aplicación que usa EF 6.0.2 con SQL Server y con .NET 4,5 o superior como destino, puede escribir lo siguiente en una ventana del símbolo del sistema (Recuerde abrirla como administrador):  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> Esto aprovecha el hecho de que la instalación de imágenes nativas para el proveedor de EF para SQL Server también instalará de forma predeterminada las imágenes nativas para el ensamblado de tiempo de ejecución de EF principal. Esto funciona porque NGen.exe puede detectar que EntityFramework.dll es una dependencia directa del ensamblado de EntityFramework.SqlServer.dll ubicado en el mismo directorio.  

## <a name="creating-native-images-during-setup"></a>Crear imágenes nativas durante la instalación  

El kit de herramientas de WiX permite poner en cola la generación de imágenes nativas para los ensamblados administrados durante la instalación, como se explica en esta [Guía de procedimientos](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html). Otra alternativa es crear una tarea de instalación personalizada que ejecute el comando NGen.exe.  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>Comprobando que las imágenes nativas se usan para EF  

Puede comprobar que una aplicación específica usa un ensamblado nativo buscando ensamblados cargados que tengan la extensión ".ni.dll" o ".ni.exe". Por ejemplo, se llamará a una imagen nativa para el ensamblado en tiempo de ejecución principal de EF EntityFramework.ni.dll. Una manera sencilla de inspeccionar los ensamblados .NET cargados de un proceso es usar el [Explorador de procesos](https://technet.microsoft.com/sysinternals/bb896653).  

## <a name="other-things-to-be-aware-of"></a>Otras cosas que debe tener en cuenta  

**No se debe confundir la creación de una imagen nativa de un ensamblado con el registro del ensamblado en la [GAC (caché global de ensamblados)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**. NGen.exe permite crear imágenes de ensamblados que no se encuentran en la GAC y, de hecho, varias aplicaciones que usan una versión específica de EF pueden compartir la misma imagen nativa. Aunque Windows 8 puede crear automáticamente imágenes nativas para los ensamblados colocados en la GAC, el tiempo de ejecución de EF se optimiza para implementarse junto con la aplicación y no se recomienda registrarlo en la GAC, ya que esto tiene un impacto negativo en la resolución de ensamblados y en el mantenimiento de las aplicaciones entre otros aspectos.  
