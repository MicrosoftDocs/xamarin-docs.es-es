---
title: ListView de Xamarin.Forms
description: Esta guía presenta el ListView de Xamarin.Forms, que se puede usar para presentar datos en listas interactivas y atractivas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: 4aae2a206f07ea6caa7fc0d7530fb9fec05ce5f0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648428"
---
# <a name="xamarinforms-listview"></a>ListView de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)es una vista para presentar listas de datos, especialmente listas largas que requieren desplazamiento.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)es una vista para presentar listas de datos con diferentes especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [Xamarin. Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casos de uso

Asegúrese de que ListView es el control adecuado para sus necesidades. ListView puede usarse en cualquier situación donde se va a mostrar desplazables listas de datos. ListView admite acciones de contexto y enlace de datos.

ListView no debe confundirse con [TableView](~/xamarin-forms/user-interface/tableview.md). El control de TableView es una mejor opción cada vez que tenga una lista no enlazada de opciones o datos. Por ejemplo, la aplicación de configuración de iOS, que tiene un conjunto de opciones predefinido en su mayoría, es más adecuada usar TableView que ListView.

También tenga en cuenta que es mejor un ListView adecuado para datos homogéneos &ndash; es decir, todos los datos deben ser del mismo tipo. Esto es porque se puede usar un único tipo de celda para cada fila de la lista. TableViews puede admitir varios tipos de celda, por lo que son una mejor opción cuando necesite combinar las vistas.

## <a name="components"></a>Componentes
ListView tiene una serie de componentes disponibles para ejercer la funcionalidad nativa de cada plataforma. Cada uno de estos componentes se describe a continuación:

- **[Encabezados y pies de página](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; texto o vista para mostrar al principio y al final de una lista, separar de los datos de la lista. Encabezados y pies de página se pueden enlazar a un origen de datos independientemente del origen de datos del ListView.
- **[Grupos](customizing-list-appearance.md#Grouping)**  &ndash; datos en un ListView se pueden agrupar para navegar más fácilmente. Normalmente, los grupos están enlazados a datos:

![](images/grouping-depth.png "ListView con datos agrupados")

- **[Las celdas](customizing-cell-appearance.md)**  &ndash; datos en un ListView se presentan en las celdas. Cada celda corresponde a una fila de datos. Hay celdas integradas que puede elegir, o puede definir su propia celda personalizada. Las celdas integradas y personalizadas pueden ser usa/definido en XAML o código.
  - **[Integrada](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; integrada en las celdas, especialmente TextCell y ImageCell, puede ser bueno para el rendimiento, puesto que corresponden a los controles nativos en cada plataforma.
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; muestra una cadena de texto, opcionalmente con detalle. Texto de detalle se representa como una segunda línea en una fuente menor con un color de énfasis.
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; muestra una imagen con texto. Aparece como un TextCell con una imagen de la izquierda.
  - **[Las celdas personalizadas](customizing-cell-appearance.md#customcells)**  &ndash; celdas personalizado son excelentes cuando se necesita para presentar datos complejos. Por ejemplo, se podría usar una vista personalizada para presentar una lista de canciones, incluido el álbum y el intérprete:

![](images/image-cell-default.png "ListView con ImageCells")

Para más información acerca de cómo personalizar las celdas de un ListView, consulte [personalizar la apariencia de una celda ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidad
ListView es compatible con un número de estilos de interacción, incluidos:

- **[Incorporación de cambios para actualizar](interactivity.md#Pull_to_Refresh)**  &ndash; ListView es compatible con la incorporación de cambios para actualizar en cada plataforma.
- **[Acciones de contexto](interactivity.md#Context_Actions)**  &ndash; ListView admite tomar las medidas en elementos individuales en una lista. Por ejemplo, puede implementar la acción de pasar el dedo en iOS, o mantenga pulsado acciones en Android.
- **[Selección](interactivity.md#selectiontaps)**  &ndash; puede escuchar las selecciones y deselections para realizar una acción cuando se pulsa una fila.

![](images/context-default.png "ListView con acciones de contexto")

Para obtener más información acerca de las características de interactividad de ListView, vea [acciones & interactividad con ListView](interactivity.md).

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Enlaces bidireccionales (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Integrado en las celdas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celdas personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [GROUPING (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interactividad de ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
