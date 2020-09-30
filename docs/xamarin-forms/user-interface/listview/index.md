---
title: Xamarin.Forms ListView
description: En esta guía se presenta el Xamarin.Forms control ListView, que se puede usar para presentar los datos en listas interactivas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fa3769d2533a5a6b482c92b832d54506e4954250
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560148"
---
# <a name="no-locxamarinforms-listview"></a>Xamarin.Forms ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView) es una vista para presentar listas de datos, especialmente listas largas que requieren desplazamiento.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista para presentar listas de datos con diferentes especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView) . Para obtener más información, vea [CollectionView de Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casos de uso

`ListView`Se puede usar un control en cualquier situación en la que se muestren listas desplazables de datos. La `ListView` clase admite acciones de contexto y enlace de datos.

El `ListView` control no debe confundirse con el [`TableView`](~/xamarin-forms/user-interface/tableview.md) control. El `TableView` control es una opción mejor cuando tiene una lista no enlazada de opciones o datos, ya que permite especificar opciones predefinidas en XAML. Por ejemplo, la aplicación de configuración de iOS, que tiene un conjunto de opciones principalmente predefinida, es más adecuada para usar un `TableView` que `ListView` .

La `ListView` clase no admite la definición de elementos de lista en XAML, debe utilizar la `ItemsSource` propiedad o el enlace de datos con `ItemTemplate` para definir los elementos de la lista.

`ListView`Es más adecuado para las colecciones que se componen de un solo tipo de datos. Este requisito se debe a que solo se puede usar un tipo de celda para cada fila de la lista. El `TableView` control puede admitir varios tipos de celda, por lo que es una opción mejor cuando necesita mostrar varios tipos de datos.

Para obtener más información sobre cómo enlazar datos a una `ListView` instancia de, vea [orígenes de datos de ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

## <a name="components"></a>Componentes

El `ListView` control tiene una serie de componentes disponibles para ejecutar la funcionalidad nativa de cada plataforma. Estos componentes se definen en las secciones siguientes.

### <a name="headers-and-footers"></a>[Encabezados y pies de página](customizing-list-appearance.md#headers-and-footers)

Los componentes de encabezado y pie de página se muestran al principio y al final de una lista, separados de los datos de la lista. Los encabezados y pies de página se pueden enlazar a un origen de datos independiente del origen de datos de ListView.

### <a name="groups"></a>[Grupos](customizing-list-appearance.md#grouping)

Los datos de un `ListView` se pueden agrupar para facilitar la navegación. Los grupos normalmente están enlazados a datos. En la captura de pantalla siguiente se muestra un `ListView` con datos agrupados:

[!["Datos agrupados en un control ListView"](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "Datos agrupados en un control ListView")

### <a name="cells"></a>[Celdas](customizing-cell-appearance.md)

Los elementos de datos de un `ListView` se denominan celdas. Cada celda corresponde a una fila de datos. Hay celdas integradas entre las que elegir, o bien puede definir su propia celda personalizada. Tanto las celdas integradas como las personalizadas pueden usarse o definirse en XAML o en código.

- [Las celdas integradas](customizing-cell-appearance.md#built-in-cells), como `TextCell` y `ImageCell` , corresponden a los controles nativos y tienen un rendimiento muy eficaz.
  - Un [`TextCell`](customizing-cell-appearance.md#textcell) muestra una cadena de texto, opcionalmente con texto detallado. El texto detallado se representa como una segunda línea en una fuente menor con un color de énfasis.
  - Un [`ImageCell`](customizing-cell-appearance.md#imagecell) muestra una imagen con texto. Aparece como un `TextCell` con una imagen a la izquierda.
- [Las celdas personalizadas](customizing-cell-appearance.md#custom-cells) se usan para presentar datos complejos. Por ejemplo, una celda personalizada podría usarse para presentar una lista de canciones que incluye el álbum y el intérprete.

En la captura de pantalla siguiente se muestra un `ListView` elemento con elementos ImageCell:

[!["Elementos ImageCell en un control ListView"](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "ImageCell elementos en un control ListView")

Para obtener más información sobre cómo personalizar las celdas de un `ListView` , vea personalizar el aspecto de las [celdas de ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidad

La `ListView` clase admite varios estilos de interacción.

- La [extracción a actualización](interactivity.md#pull-to-refresh) permite al usuario extraer el `ListView` contenido para actualizar el contenido.
- [Las acciones de contexto](interactivity.md#context-actions) permiten al desarrollador especificar acciones personalizadas en elementos individuales de la lista. Por ejemplo, puede implementar el deslizamiento a la acción en iOS o en las acciones de punteo largo en Android.
- La [selección](interactivity.md#selection-and-taps) permite al desarrollador adjuntar la funcionalidad a los eventos de selección y anulación de selección en los elementos de la lista.

En la captura de pantalla siguiente se muestra un `ListView` con acciones de contexto:

[!["Acciones de contexto en un control ListView"](images/context-default-cropped.png)](images/context-default.png#lightbox "Acciones de contexto en un control ListView")

Para obtener más información sobre las características de interactividad de `ListView` , vea [acciones & interactividad con ListView](interactivity.md).

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con ListView (ejemplo)](/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [Enlace bidireccional (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [Celdas integradas (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celdas personalizadas (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Agrupación (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Vista de representador personalizado (ejemplo)](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [Interactividad de ListView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)