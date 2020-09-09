---
title: Compatibilidad con enum-Code First-EF6
description: 'Compatibilidad con enum: Code First en Entity Framework 6'
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: 2ed5cd73f8477469d3651ef4bf9f79ce80598ee7
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617176"
---
# <a name="enum-support---code-first"></a>Compatibilidad con enum-Code First
> [!NOTE]
> **EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

Este tutorial de vídeo y paso a paso muestra cómo utilizar tipos de enumeración con Entity Framework Code First. También se muestra cómo usar las enumeraciones en una consulta LINQ.

En este tutorial se utilizará Code First para crear una nueva base de datos, pero también puede usar [code First para asignarla a una base de datos existente](xref:ef6/modeling/code-first/workflows/existing-database).

La compatibilidad con la enumeración se presentó en Entity Framework 5. Para usar las nuevas características, como las enumeraciones, los tipos de datos espaciales y las funciones con valores de tabla, debe tener como destino .NET Framework 4,5. Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.

En Entity Framework, una enumeración puede tener los siguientes tipos subyacentes: **byte**, **Int16**, **Int32**, **Int64** o **SByte**.

## <a name="watch-the-video"></a>Visualización del vídeo
En este vídeo se muestra cómo utilizar tipos de enumeración con Code First de Entity Framework. También se muestra cómo usar las enumeraciones en una consulta LINQ.

**Presentada por**: Julia Kornich

**Vídeo**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>Requisitos previos

Deberá tener instalado Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition para completar este tutorial.

 

## <a name="set-up-the-project"></a>Configurar el proyecto

1.  Abra Visual Studio 2012
2.  En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .
3.  En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
4.  Escriba **EnumCodeFirst** como nombre del proyecto y haga clic en **Aceptar** .

## <a name="define-a-new-model-using-code-first"></a>Definir un nuevo modelo mediante Code First

Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio). El código siguiente define la clase Department.

El código también define la enumeración DepartmentNames. De forma predeterminada, la enumeración es de tipo **int** . La propiedad Name de la clase Department es del tipo DepartmentNames.

Abra el archivo Program.cs y pegue las siguientes definiciones de clase.

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a>Definir el tipo derivado de DbContext

Además de definir entidades, debe definir una clase que derive de DbContext y exponga las propiedades DbSet&lt;TEntity&gt;. Las propiedades DbSet&lt;TEntity&gt; permiten que el contexto sepa qué tipos desea incluir en el modelo.

Una instancia del tipo derivado de DbContext administra los objetos de entidad durante el tiempo de ejecución, lo que incluye rellenar los objetos con datos de una base de datos, el seguimiento de cambios y la persistencia de datos en la base de datos.

Los tipos DbContext y DbSet se definen en el ensamblado EntityFramework. Se agregará una referencia a este archivo DLL mediante el paquete NuGet EntityFramework.

1.  En Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto.
2.  Seleccione **administrar paquetes NuGet..** .
3.  En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework** .
4.  Haz clic en **Instalar**

Tenga en cuenta que, además del ensamblado EntityFramework, también se agregan las referencias a los ensamblados System. ComponentModel. DataAnnotations y System. Data. Entity.

En la parte superior del archivo Program.cs, agregue la siguiente instrucción using:

``` csharp
using System.Data.Entity;
```

En Program.cs, agregue la definición de contexto. 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>Conservar y recuperar datos

Abra el archivo Program.cs en el que se define el método Main. Agregue el código siguiente a la función main. El código agrega un nuevo objeto Department al contexto. Después guarda los datos. El código también ejecuta una consulta LINQ que devuelve un departamento en el que el nombre es DepartmentNames. English.

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Compile y ejecute la aplicación. El programa produce el siguiente resultado:

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>Ver la base de datos generada

Al ejecutar la aplicación por primera vez, el Entity Framework crea una base de datos automáticamente. Dado que tenemos instalado Visual Studio 2012, la base de datos se creará en la instancia de LocalDB. De forma predeterminada, el Entity Framework nombra la base de datos después del nombre completo del contexto derivado (para este ejemplo, que es **EnumCodeFirst. EnumTestContext**). Las veces posteriores en las que se utilizará la base de datos existente.  

Tenga en cuenta que si realiza cambios en el modelo una vez creada la base de datos, debe utilizar Migraciones de Code First para actualizar el esquema de la base de datos. Vea [code First en una nueva base de datos](xref:ef6/modeling/code-first/workflows/new-database) para obtener un ejemplo del uso de las migraciones.

Para ver la base de datos y los datos, haga lo siguiente:

1.  En el menú principal de Visual Studio 2012, seleccione **Ver**  - &gt; **Explorador de objetos de SQL Server**.
2.  Si LocalDB no está en la lista de servidores, haga clic con el botón secundario del mouse en **SQL Server** y seleccione **Agregar SQL Server** usar la **autenticación de Windows** predeterminada para conectarse a la instancia de LocalDB.
3.  Expandir el nodo LocalDB
4.  Desabra la carpeta **bases** de datos para ver la nueva base de datos y vaya a la tabla **Department** . tenga en cuenta que Code First no crea una tabla que se asigne al tipo de enumeración
5.  Para ver los datos, haga clic con el botón derecho en la tabla y seleccione **ver datos** .

## <a name="summary"></a>Resumen

En este tutorial, hemos visto cómo usar los tipos de enumeración con Entity Framework Code First. 
