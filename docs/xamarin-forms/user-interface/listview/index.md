---
title: ListView de Xamarin.Forms
description: En esta guía se presenta el control ListView de Xamarin. Forms, que se puede usar para presentar los datos en listas interactivas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
ms.openlocfilehash: 5d09d76a44a6322285a143230173d244848ba4a6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770224"
---
# <a name="xamarinforms-listview"></a>ListView de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)es una vista para presentar listas de datos, especialmente listas largas que requieren desplazamiento.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista para presentar listas de datos con diferentes especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView). Para obtener más información, vea [CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casos de uso

Se `ListView` puede usar un control en cualquier situación en la que se muestren listas desplazables de datos. La `ListView` clase admite acciones de contexto y enlace de datos.

El `ListView` control no debe confundirse con [`TableView`](~/xamarin-forms/user-interface/tableview.md) el control. El `TableView` control es una opción mejor cuando tiene una lista no enlazada de opciones o datos, ya que permite especificar opciones predefinidas en XAML. Por ejemplo, la aplicación de configuración de iOS, que tiene un conjunto de opciones principalmente predefinida, es más adecuada para `TableView` usar un `ListView`que.

La `ListView` clase no admite la definición de elementos de lista en XAML, debe `ItemsSource` utilizar la propiedad `ItemTemplate` o el enlace de datos con para definir los elementos de la lista.

`ListView` Es más adecuado para las colecciones que se componen de un solo tipo de datos. Este requisito se debe a que solo se puede usar un tipo de celda para cada fila de la lista. El `TableView` control puede admitir varios tipos de celda, por lo que es una opción mejor cuando necesita mostrar varios tipos de datos.

Para obtener más información sobre cómo enlazar `ListView` datos a una instancia de, vea [orígenes de datos de ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Componentes
El `ListView` control tiene una serie de componentes disponibles para ejecutar la funcionalidad nativa de cada plataforma. Estos componentes se definen en las secciones siguientes.

### <a name="headers-and-footerscustomizing-list-appearancemdheaders-and-footers"></a>[Encabezados y pies de página](customizing-list-appearance.md#headers-and-footers)

Los componentes de encabezado y pie de página se muestran al principio y al final de una lista, separados de los datos de la lista. Los encabezados y pies de página se pueden enlazar a un origen de datos independiente del origen de datos de ListView.

### <a name="groupscustomizing-list-appearancemdgrouping"></a>[Familias](customizing-list-appearance.md#grouping)

Los datos de `ListView` un se pueden agrupar para facilitar la navegación. Los grupos normalmente están enlazados a datos. En la captura de pantalla `ListView` siguiente se muestra un con datos agrupados:

"Datos agrupados [en un control ListView" agrupados en un control ListView ![](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "")

### <a name="cellscustomizing-cell-appearancemd"></a>[Celdas](customizing-cell-appearance.md)

Los elementos de datos `ListView` de un se denominan celdas. Cada celda corresponde a una fila de datos. Hay celdas integradas que puede elegir, o puede definir su propia celda personalizada. Las celdas integradas y personalizadas pueden ser usa/definido en XAML o código.

- [Las celdas integradas](customizing-cell-appearance.md#built-in-cells), `TextCell` como y `ImageCell`, corresponden a los controles nativos y tienen un rendimiento muy eficaz.
  - Un [`TextCell`](customizing-cell-appearance.md#textcell) muestra una cadena de texto, opcionalmente con texto detallado. Texto de detalle se representa como una segunda línea en una fuente menor con un color de énfasis.
  - Un [`ImageCell`](customizing-cell-appearance.md#imagecell) muestra una imagen con texto. Aparece como un `TextCell` con una imagen a la izquierda.
- [Las celdas personalizadas](customizing-cell-appearance.md#customcells) se usan para presentar datos complejos. Por ejemplo, una celda personalizada podría usarse para presentar una lista de canciones que incluye el álbum y el intérprete.

En la captura de pantalla `ListView` siguiente se muestra un elemento con elementos ImageCell:

(images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "Elementos de ImageCell") de [elementos ImageCell en un control ListView ![]

Para obtener más información sobre cómo personalizar las celdas `ListView`de un, vea personalizar el aspecto de las [celdas de ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidad
La `ListView` clase admite varios estilos de interacción.

- La [extracción a actualización](interactivity.md#pull-to-refresh) permite al usuario extraer el `ListView` contenido para actualizar el contenido.
- [Las acciones de contexto](interactivity.md#context-actions) permiten al desarrollador especificar acciones personalizadas en elementos individuales de la lista. Por ejemplo, puede implementar la acción de pasar el dedo en iOS, o mantenga pulsado acciones en Android.
- La [selección](interactivity.md#selectiontaps) permite al desarrollador adjuntar la funcionalidad a los eventos de selección y anulación de selección en los elementos de la lista.

En la captura de pantalla `ListView` siguiente se muestra un con acciones de contexto:

Acciones de contexto ["acciones de contexto en un control ListView" en un control ListView ![](images/context-default-cropped.png)](images/context-default.png#lightbox "")

Para obtener más información sobre las características de interactividad de `ListView`, vea [acciones & interactividad con ListView](interactivity.md).

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Enlaces bidireccionales (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Integrado en las celdas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celdas personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [GROUPING (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interactividad de ListView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
