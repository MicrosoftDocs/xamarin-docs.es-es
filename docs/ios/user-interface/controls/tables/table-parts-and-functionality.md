---
title: Elementos de tabla y funcionalidad de Xamarin. iOS
description: En este documento se describen las distintas partes de un UITableView en iOS. Se describen los encabezados de sección, las celdas, los pies de sección, el índice y el modo de edición.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 0e8014263d417cef39147b440ed3b38155f226bb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021854"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Elementos de tabla y funcionalidad de Xamarin. iOS

Un UITableView puede tener un estilo "agrupado" o "sin formato", y consta de las siguientes partes:

- [Encabezado de sección](#Section_Header)
- [Celdas](#Cells) (o filas, si lo prefiere)
- [Pie de sección](#Section_Footer)
- [Index](#Index)
- [Modo de edición](#Edit_Features) (incluye ' deslizar rápidamente a eliminar ' y arrastrar Controladores para cambiar el orden de las filas) 

Estas capturas de pantalla muestran cómo se muestran las filas, los encabezados, los pies de página, los controles de edición y el índice de la sección.

 [![](table-parts-and-functionality-images/image1a.png "These screenshots show how section rows, headers, footers, edit controls and the index are displayed")](table-parts-and-functionality-images/image1a.png#lightbox)

Estas partes se describen con más detalle a continuación:

<a name="Section_Header" />

## <a name="section-header"></a>Encabezado de sección

Opcionalmente, las celdas pueden agruparse en secciones, etiquetadas con un encabezado personalizado o etiquetadas con un pie de página. El encabezado se puede establecer con un valor de cadena o se puede proporcionar una vista personalizada para permitir un diseño o estilo diferente.

<a name="Cells" />

## <a name="cells"></a>Celdas

Las celdas son el elemento principal de la interfaz de usuario de una tabla. Cuando se implementa correctamente, las celdas se reutilizan para la eficacia de la memoria. Hay cuatro estilos de celda integrados, y puede crear sus propias celdas personalizadas (ya sea en el código o en el diseñador al usar guiones gráficos).

<a name="Section_Footer"/>

## <a name="section-footer"></a>Pie de sección

El pie de sección opcional se puede establecer con un valor de cadena, o bien se puede proporcionar una vista personalizada para permitir un diseño o estilo diferente. Los encabezados y pies de página se pueden establecer de forma independiente.

<a name="Index" />

## <a name="index"></a>Índice

El índice aparece como una franja de caracteres en el borde derecho de la tabla.
Al tocar o arrastrar en el índice se acelera el desplazamiento a esa parte de la tabla. Un índice es opcional, pero se recomienda que ayude a navegar por listas largas. Normalmente, un índice no se utiliza con el estilo agrupado.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Modo de edición

Hay un par de características de edición diferentes disponibles:

- Deslice el dedo para eliminar celdas individuales.
- Entrar en el modo de edición para mostrar los botones de eliminación en cada fila 
- Entrando en el modo de edición para mostrar los identificadores de reordenación. 
- Insertar nuevas celdas (con animación).

En el resto de este documento se muestra cómo implementar todas estas características de UITableView con Xamarin. iOS.

## <a name="classes-overview"></a>Información general sobre clases

A continuación se muestran las clases principales que se usan para mostrar las vistas de tabla:

[![](table-parts-and-functionality-images/classdiagram.png "The primary classes used to display table views are shown here")](table-parts-and-functionality-images/classdiagram.png#lightbox)

A continuación se describe el propósito de cada clase:

- **UITableView** : una vista que contiene una colección de celdas dentro de un contenedor de desplazamiento. La vista de tabla normalmente usa toda la pantalla en una aplicación de iPhone, pero puede existir como parte de una vista más grande en el iPad (o aparecer en un elemento flotante). 
- **UITableViewCell** : una vista que representa una sola celda (o fila) en una vista de tabla. Hay cuatro tipos de celdas integrados y es posible crear celdas personalizadas en C# o con iOS Designer. 
- **UITableViewSource** – Xamarin. iOS: clase abstracta exclusiva que proporciona todos los métodos necesarios para mostrar una tabla, incluido el recuento de filas, la devolución de una vista de celda para cada fila, el control de la selección de filas y muchas otras características opcionales. *Debe* subclase para que funcione UITableView. 
- **NSIndexPath** : contiene las propiedades de fila y de sección que identifican de forma única la posición de una celda en una tabla. 
- **UITableViewController** : un UIViewController listo para usar que tiene un UITableView codificado de forma rígida como su vista y accesible a través de la propiedad TableView. 
- **UIViewController** : Si la tabla no ocupa toda la pantalla, puede Agregar un UITableView a cualquier UIViewController con su marco establecido adecuadamente. 

UITableViewSource reemplaza las dos clases siguientes, que siguen estando disponibles en Xamarin. iOS pero normalmente no son necesarias:

- **UITableViewDataSource** : protocolo Objective-C que se modela en Xamarin. iOS como una clase abstracta. Se debe crear una subclase para proporcionar una tabla con una vista para cada celda, así como información acerca de los encabezados, pies de página y el número de filas y secciones de la tabla. 
- **UITableViewDelegate** : protocolo Objective-C que se modela en Xamarin. iOS como una clase. Controla la selección, las características de edición y otras características de tabla opcionales. 

En este documento, todos los ejemplos usan UITableViewSource y omiten estas dos clases. Se mencionan aquí porque todos los ejemplos de Objective-C que se encuentran en la documentación de Apple hacen referencia a ellos, por lo que resulta útil entender lo que hacen (y que puede usar el UITableViewSource de Xamarin. iOS en su lugar).

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
