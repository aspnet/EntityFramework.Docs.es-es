---
title: Entity Framework Glosario-EF6
description: Entity Framework 6 Glosario
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: 19d5e9e3a480337c2bcb93be5f989cc622b67dad
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620196"
---
# <a name="entity-framework-glossary"></a>Entity Framework Glosario
## <a name="code-first"></a>Code First
Crear un modelo de Entity Framework mediante código. El modelo puede tener como destino una base de datos existente o una nueva.

## <a name="context"></a>Contexto
Una clase que representa una sesión con la base de datos, lo que permite consultar y guardar datos. Un contexto se deriva de la clase DbContext o ObjectContext.

## <a name="convention-code-first"></a>Convención (Code First)
Una regla que Entity Framework usa para inferir la forma de su modelo a partir de las clases.

## <a name="database-first"></a>Database First
Crear un modelo de Entity Framework, mediante el diseñador de EF, que tiene como destino una base de datos existente.

## <a name="eager-loading"></a>Carga diligente
Patrón de carga de datos relacionados donde una consulta para un tipo de entidad también carga las entidades relacionadas como parte de la consulta.

## <a name="ef-designer"></a>EF Designer
Diseñador visual de Visual Studio que permite crear un modelo de Entity Framework mediante cuadros y líneas.

## <a name="entity"></a>Entidad
Clase u objeto que representa datos de aplicación como clientes, productos y pedidos.

## <a name="entity-data-model"></a>Entity Data Model
Modelo que describe las entidades y las relaciones entre ellas. EF usa EDM para describir el modelo conceptual en el que los programas de desarrollador. EDM se basa en el modelo de relación de entidades introducido por Dr. Peter Chen. El EDM se desarrolló originalmente con el objetivo principal de convertirse en el modelo de datos común en un conjunto de tecnologías de desarrollador y de servidor de Microsoft. EDM también se usa como parte del protocolo OData.

## <a name="explicit-loading"></a>Carga explícita
Patrón de carga de datos relacionados en los que los objetos relacionados se cargan llamando a una API.

## <a name="fluent-api"></a>API fluida
Una API que se puede usar para configurar un modelo de Code First.

## <a name="foreign-key-association"></a>Asociación de clave externa
Asociación entre entidades donde una propiedad que representa la clave externa se incluye en la clase de la entidad dependiente. Por ejemplo, Product contiene una propiedad CategoryId.

## <a name="identifying-relationship"></a>Relación de identificación
Relación donde la clave principal de la entidad principal también forma parte de la clave principal de la entidad dependiente. En este tipo de relación, la entidad dependiente no puede existir sin la entidad principal.

## <a name="independent-association"></a>Asociación independiente
Asociación entre entidades en las que no hay ninguna propiedad que represente la clave externa en la clase de la entidad dependiente. Por ejemplo, una clase de producto contiene una relación con la categoría pero no tiene la propiedad CategoryId. Entity Framework realiza un seguimiento del estado de la Asociación independientemente del estado de las entidades en los dos extremos de la asociación.

## <a name="lazy-loading"></a>Carga diferida
Patrón de carga de datos relacionados en los que los objetos relacionados se cargan automáticamente cuando se tiene acceso a una propiedad de navegación.

## <a name="model-first"></a>Model First
Crear un modelo de Entity Framework, mediante el diseñador de EF, que se utiliza para crear una nueva base de datos.

## <a name="navigation-property"></a>Propiedad de navegación
Propiedad de una entidad que hace referencia a otra entidad. Por ejemplo, el producto contiene una propiedad de navegación categoría y la categoría contiene una propiedad de navegación productos.

## <a name="poco"></a>POCO
Acrónimo para el objeto CLR antiguo. Una clase de usuario simple que no tiene dependencias con ningún marco de trabajo. En el contexto de EF, una clase de entidad que no se deriva de EntityObject, implementa las interfaces o incluye cualquier atributo definido en EF. Estas clases de entidad que se desacoplan del marco de persistencia también se consideran "que ignoran la persistencia".  

## <a name="relationship-inverse"></a>Inverso de relación
Extremo opuesto de una relación, por ejemplo, product. Categoría y categoría. Manuales.

## <a name="self-tracking-entity"></a>Entidad de seguimiento propio
Una entidad creada a partir de una plantilla de generación de código que ayuda con el desarrollo de N niveles.

## <a name="table-per-concrete-type-tpc"></a>Tabla por tipo concreto (TPC)
Método de asignación de la herencia en la que cada tipo no abstracto de la jerarquía se asigna a una tabla independiente en la base de datos.

## <a name="table-per-hierarchy-tph"></a>Tabla por jerarquía (TPH)
Método de asignación de la herencia en la que todos los tipos de la jerarquía se asignan a la misma tabla de la base de datos. Se utiliza una o varias columnas de discriminador para identificar a qué tipo está asociada cada fila.

## <a name="table-per-type-tpt"></a>Tabla por tipo (TPT)
Método de asignación de la herencia en la que las propiedades comunes de todos los tipos de la jerarquía se asignan a la misma tabla de la base de datos, pero las propiedades exclusivas de cada tipo se asignan a una tabla independiente.

## <a name="type-discovery"></a>Detección de tipos
El proceso de identificar los tipos que deben formar parte de un modelo de Entity Framework.
