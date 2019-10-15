---
title: Introducción a la CollectionView de Xamarin. Forms
description: La CollectionView es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 89afb0f2bfe93a5f78b0cd162f2a65e585b54b4b
ms.sourcegitcommit: 43423d4018cc0d4b0b8c98a4b3da0704495eb0cf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303233"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introducción a la CollectionView de Xamarin. Forms

![Esta API está actualmente en versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista para presentar listas de datos con diferentes especificaciones de diseño. Pretende proporcionar una alternativa más flexible y eficaz a [`ListView`](xref:Xamarin.Forms.ListView). Por ejemplo, las siguientes capturas de pantallas muestran un `CollectionView` que usa una cuadrícula vertical de dos columnas y que permite la selección múltiple:

[![Captura de pantalla de un diseño de cuadrícula vertical de CollectionView, en el diseño de cuadrículas verticales de la CollectionView de iOS y Android](introduction-images/verticalgrid-multipleselection.png "con selección múltiple")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Diseño de cuadrícula vertical de CollectionView con selección múltiple")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) está disponible en Xamarin. forms 4,0. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la clase `AppDelegate` en iOS, o bien a la clase `MainActivity` en Android o a la `App.xaml.cs` en UWP antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) está disponible en iOS y Android, pero solo está [disponible parcialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) en el plataforma universal de Windows.

## <a name="collectionview-and-listview-differences"></a>Diferencias de CollectionView y ListView

Aunque las API [`CollectionView`](xref:Xamarin.Forms.CollectionView) y [`ListView`](xref:Xamarin.Forms.ListView) son similares, existen algunas diferencias notables:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un modelo de diseño flexible, que permite presentar los datos vertical u horizontalmente, en una lista o una cuadrícula.
- [`CollectionView` admite la](xref:Xamarin.Forms.CollectionView) selección única y múltiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) no tiene concepto de celdas. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos de la lista.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) emplea automáticamente la virtualización proporcionada por los controles nativos subyacentes.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) reduce la superficie de API de [`ListView`](xref:Xamarin.Forms.ListView). Muchas propiedades y eventos de [`ListView`](xref:Xamarin.Forms.ListView) no están presentes en `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) no incluye separadores integrados.

## <a name="move-from-listview-to-collectionview"></a>Pasar de ListView a CollectionView

las implementaciones de [`ListView`](xref:Xamarin.Forms.ListView) en implementaciones de Xamarin. Forms existentes se pueden migrar a implementaciones de [`CollectionView`](xref:Xamarin.Forms.CollectionView) con la ayuda de la siguiente tabla:

| Concepto | API de ListView | CollectionView |
|---|---|---|
| Datos | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su propiedad `ItemsSource`. Para obtener más información, vea [rellenar una CollectionView con datos](populate-data.md#populate-a-collectionview-with-data). |
| Apariencia del elemento | `ItemTemplate` | La apariencia de cada elemento de un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se puede definir estableciendo la propiedad `ItemTemplate` en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obtener más información, vea [definir la apariencia](populate-data.md#define-item-appearance)de los elementos. |
| Celdas | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) no tiene concepto de celdas. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos de la lista. |
| Separadores de filas | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) no incluye separadores integrados. Se pueden proporcionar, si se desea, en la plantilla de elemento. |
| Selección | `SelectionMode`, `SelectedItem` | [`CollectionView` admite la](xref:Xamarin.Forms.CollectionView) selección única y múltiple. Para obtener más información, consulte [selección de CollectionView de Xamarin. Forms](selection.md). |
| Alto de fila | `HasUnevenRows`, `RowHeight` | En un `CollectionView`, el alto de la fila de cada elemento viene determinado por la propiedad `ItemSizingStrategy`. Para obtener más información, vea tamaño de los [elementos](layout.md#item-sizing).|
| Almacenamiento en memoria caché | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa automáticamente la virtualización proporcionada por los controles nativos subyacentes. |
| Encabezados y pies de página | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) puede presentar un encabezado y un pie de página que se desplacen con los elementos de la lista, a través de las propiedades `Header`, `Footer`, `HeaderTemplate` y `FooterTemplate`. Para obtener más información, vea [encabezados y pies de página](layout.md#headers-and-footers). |
| Agrupar | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra los datos agrupados correctamente estableciendo su propiedad `IsGrouped` en `true`. Los encabezados de grupo y los pies de grupo se pueden personalizar si se establecen las propiedades `GroupHeaderTemplate` y `GroupFooterTemplate` en objetos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Para obtener más información, vea GROUPING de la agrupación de datos de [Xamarin. Forms](grouping.md). |
| Extraer para actualizar | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | La incorporación de cambios a la actualización no se admite actualmente en `CollectionView`, pero se agregará en una versión futura. |
| Acciones de contexto | `ContextActions` | Actualmente no se admiten acciones de contexto en `CollectionView`, pero se agregarán en una versión futura. |
| Desplazamiento | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) define los métodos `ScrollTo` que desplazan los elementos a la vista. Para obtener más información, vea [desplazamiento](scrolling.md). |

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
