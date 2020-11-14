---
title: Xamarin.Forms Introducción a CollectionView
description: La CollectionView es una alternativa flexible y eficaz a ListView.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ad9ef36471b14ad44b108189b94bb22d1b3db41
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590264"
---
# <a name="no-locxamarinforms-collectionview-introduction"></a>Xamarin.Forms Introducción a CollectionView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista para presentar listas de datos con diferentes especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView) . Por ejemplo, las siguientes capturas de pantallas muestran un `CollectionView` que usa una cuadrícula vertical de dos columnas y que permite la selección múltiple:

[![Captura de pantalla de un diseño de cuadrícula vertical de CollectionView, en iOS y Android](introduction-images/verticalgrid-multipleselection.png "Diseño de cuadrícula vertical de CollectionView con selección múltiple")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Diseño de cuadrícula vertical de CollectionView con selección múltiple")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) debe usarse para presentar listas de datos que requieren desplazamiento o selección. Se puede usar un diseño enlazable cuando los datos que se van a mostrar no requieren desplazamiento o selección. Para obtener más información, vea [diseños enlazables en Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) está disponible en Xamarin.Forms 4,3.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) está disponible en iOS y Android, pero solo está [disponible parcialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) en el plataforma universal de Windows.

## <a name="collectionview-and-listview-differences"></a>Diferencias de CollectionView y ListView

Aunque las [`CollectionView`](xref:Xamarin.Forms.CollectionView) [`ListView`](xref:Xamarin.Forms.ListView) API y son similares, existen algunas diferencias notables:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un modelo de diseño flexible, que permite presentar los datos vertical u horizontalmente, en una lista o una cuadrícula.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) admite la selección única y múltiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) no tiene concepto de celdas. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos de la lista.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) emplea automáticamente la virtualización proporcionada por los controles nativos subyacentes.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) reduce la superficie de la API de [`ListView`](xref:Xamarin.Forms.ListView) . Muchas propiedades y eventos de [`ListView`](xref:Xamarin.Forms.ListView) no están presentes en `CollectionView` .
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) no incluye separadores integrados.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) producirá una excepción si [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) se actualiza fuera del subproceso de la interfaz de usuario.

## <a name="move-from-listview-to-collectionview"></a>Pasar de ListView a CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) las implementaciones en Xamarin.Forms aplicaciones existentes se pueden migrar a [`CollectionView`](xref:Xamarin.Forms.CollectionView) implementaciones con la ayuda de la tabla siguiente:

| Concepto | API de las vistas de lista | CollectionView |
|---|---|---|
| data | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su `ItemsSource` propiedad. Para obtener más información, vea [rellenar una CollectionView con datos](populate-data.md#populate-a-collectionview-with-data). |
| Apariencia del elemento | `ItemTemplate` | La apariencia de cada elemento de un [`CollectionView`](xref:Xamarin.Forms.CollectionView) puede definirse estableciendo la `ItemTemplate` propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Para obtener más información, vea [definir la apariencia](populate-data.md#define-item-appearance)de los elementos. |
| Celdas | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) no tiene concepto de celdas y, por lo tanto, no hay ningún concepto de indicadores de divulgación. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos de la lista. |
| Separadores de filas | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) no incluye separadores integrados. Se pueden proporcionar, si se desea, en la plantilla de elemento. |
| Selección | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) admite la selección única y múltiple. Para obtener más información, consulte [ Xamarin.Forms selección de CollectionView](selection.md). |
| Alto de fila | `HasUnevenRows`, `RowHeight` | En `CollectionView` , el alto de la fila de cada elemento viene determinado por la `ItemSizingStrategy` propiedad. Para obtener más información, vea tamaño de los [elementos](layout.md#item-sizing).|
| Almacenamiento en memoria caché | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) utiliza automáticamente la virtualización proporcionada por los controles nativos subyacentes. |
| Encabezados y pies de página | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) puede presentar un encabezado y un pie de página que se desplacen con los elementos de la lista, a través de las `Header` `Footer` propiedades,, `HeaderTemplate` y `FooterTemplate` . Para obtener más información, vea [encabezados y pies de página](layout.md#headers-and-footers). |
| Agrupar | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra datos agrupados correctamente estableciendo su `IsGrouped` propiedad en `true` . Los encabezados de grupo y los pies de grupo se pueden personalizar estableciendo `GroupHeaderTemplate` las `GroupFooterTemplate` propiedades y en  [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos. Para obtener más información, consulte [ Xamarin.Forms agrupación de CollectionView](grouping.md). |
| Extraer para actualizar | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | La funcionalidad de extracción para actualizar se admite al establecer [`CollectionView`](xref:Xamarin.Forms.CollectionView) como el elemento secundario de `RefreshView` . Para obtener más información, consulte [incorporación de cambios a la actualización](populate-data.md#pull-to-refresh). |
| Elementos del menú contextual | `ContextActions` | Los elementos de menú contextual se admiten al establecer `SwipeView` como la vista raíz en el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de cada elemento de datos en [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Para más información, consulte [Menús contextuales](populate-data.md#context-menus). |
| Desplazarse | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) define `ScrollTo` métodos que desplazan los elementos en la vista. Para obtener más información, vea [desplazamiento](scrolling.md). |

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Diseños enlazables en Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
