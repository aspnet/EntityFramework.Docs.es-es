---
title: 'Compatibilidad del proveedor con tipos espaciales: EF6'
description: Compatibilidad del proveedor con los tipos espaciales en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: d6214ec503af8ea02b586cfddfd958ec7b47e42f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070450"
---
# <a name="provider-support-for-spatial-types"></a>Compatibilidad del proveedor con tipos espaciales
Entity Framework admite el trabajo con datos espaciales a través de las clases DbGeography o DbGeometry. Estas clases se basan en la funcionalidad específica de la base de datos que ofrece el proveedor de Entity Framework. No todos los proveedores admiten datos espaciales y los que pueden tener requisitos previos adicionales, como la instalación de ensamblados de tipo espacial. A continuación se proporciona más información sobre la compatibilidad del proveedor con los tipos espaciales.  

Puede encontrar información adicional sobre cómo usar los tipos espaciales en una aplicación en dos tutoriales, uno para Code First, el otro para Database First o Model First:  

- [Tipos de datos espaciales en Code First](xref:ef6/modeling/code-first/data-types/spatial)  
- [Tipos de datos espaciales en EF Designer](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a>Versiones de EF que admiten tipos espaciales  

La compatibilidad con los tipos espaciales se presentó en EF5. Sin embargo, en los tipos espaciales de EF5 solo se admiten cuando la aplicación tiene como destino y se ejecuta en .NET 4,5.  

A partir de EF6, se admiten los tipos espaciales para las aplicaciones que tienen como destino .NET 4 y .NET 4,5.  

## <a name="ef-providers-that-support-spatial-types"></a>Proveedores de EF que admiten tipos espaciales  

### <a name="ef5"></a>EF5  

Los proveedores de Entity Framework para EF5 que somos conscientes de que admiten tipos espaciales son:  

- Proveedor de Microsoft SQL Server  
    - Este proveedor se incluye como parte de EF5.  
    - Este proveedor depende de algunas bibliotecas adicionales de bajo nivel que puedan ser necesarias para instalarse; consulte a continuación para obtener más información.  
- [Devart dotConnect para Oracle](https://www.devart.com/dotconnect/oracle/)  
    - Se trata de un proveedor de terceros de Devart.  

Si conoce un proveedor de EF5 que admite tipos espaciales, póngase en contacto con y nos alegrará de agregarlo a esta lista.  

### <a name="ef6"></a>EF6  

Los proveedores de Entity Framework para EF6 que somos conscientes de que admiten tipos espaciales son:  

- Proveedor de Microsoft SQL Server  
    - Este proveedor se incluye como parte de EF6.  
    - Este proveedor depende de algunas bibliotecas adicionales de bajo nivel que puedan ser necesarias para instalarse; consulte a continuación para obtener más información.  
- [Devart dotConnect para Oracle](https://www.devart.com/dotconnect/oracle/)  
    - Se trata de un proveedor de terceros de Devart.  

Si conoce un proveedor de EF6 que admite tipos espaciales, póngase en contacto con y nos alegrará de agregarlo a esta lista.  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>Requisitos previos para los tipos espaciales con Microsoft SQL Server  

SQL Server compatibilidad espacial depende de los tipos de bajo nivel, específicos de SQL Server SqlGeography y SqlGeometry. Estos tipos viven en Microsoft.SqlServer.Types.dll ensamblado, y este ensamblado no se envía como parte de EF o como parte de la .NET Framework.  

Cuando se instala Visual Studio, a menudo también se instala una versión de SQL Server, lo que incluye la instalación de la Microsoft.SqlServer.Types.dll.  

Si SQL Server no está instalado en el equipo en el que desea usar tipos espaciales, o si los tipos espaciales se excluyen de la instalación de SQL Server, deberá instalarlos manualmente. Los tipos se pueden instalar mediante `SQLSysClrTypes.msi` , que forma parte de Microsoft SQL Server Feature Pack. Los tipos espaciales son SQL Server específicos de la versión, por lo que se recomienda [Buscar "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) en el centro de descarga de Microsoft y, a continuación, seleccionar y descargar la opción correspondiente a la versión de SQL Server que se va a usar.
