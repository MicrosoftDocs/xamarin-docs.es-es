---
title: Introducción a la CollectionView de Xamarin. Forms
description: La CollectionView es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 14abf2e7eff64d2e3e9656bf1ca76f4cee615408
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "69986081"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introducción a la CollectionView de Xamarin. Forms

![Esta API está actualmente en versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista para presentar listas de datos con diferentes especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView). Por ejemplo, las siguientes capturas de `CollectionView` pantallas muestran un que usa una cuadrícula vertical de dos columnas y que permite la selección múltiple:

[![Captura de pantalla de un diseño de cuadrícula vertical de CollectionView, en iOS y Android](introduction-images/verticalgrid-multipleselection.png "Diseño de cuadrícula vertical de CollectionView con selección múltiple")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Diseño de cuadrícula vertical de CollectionView con selección múltiple")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)está disponible en Xamarin. Forms 4,0. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la `AppDelegate` clase en iOS, o bien a `MainActivity` la clase en Android, antes `Forms.Init`de llamar a:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)está disponible en iOS y Android, pero solo está [disponible parcialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) en el plataforma universal de Windows.

## <a name="collectionview-and-listview-differences"></a>Diferencias de CollectionView y ListView

Aunque las [`CollectionView`](xref:Xamarin.Forms.CollectionView) API [`ListView`](xref:Xamarin.Forms.ListView) y son similares, existen algunas diferencias notables:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)tiene un modelo de diseño flexible, que permite presentar los datos vertical u horizontalmente, en una lista o una cuadrícula.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)admite la selección única y múltiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)no tiene concepto de celdas. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos de la lista.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)emplea automáticamente la virtualización proporcionada por los controles nativos subyacentes.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)reduce la superficie de la [`ListView`](xref:Xamarin.Forms.ListView)API de. Muchas propiedades y eventos de [`ListView`](xref:Xamarin.Forms.ListView) no están presentes en `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)no incluye separadores integrados.

## <a name="move-from-listview-to-collectionview"></a>Pasar de ListView a CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)las implementaciones en implementaciones de Xamarin. Forms existentes se pueden [`CollectionView`](xref:Xamarin.Forms.CollectionView) migrar a implementaciones con la ayuda de la siguiente tabla:

| Concepto | API de ListView | CollectionView |
|---|---|---|
| Datos | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su `ItemsSource` propiedad. Para obtener más información, vea [rellenar una CollectionView con datos](populate-data.md#populate-a-collectionview-with-data). |
| Apariencia del elemento | `ItemTemplate` | La apariencia de cada elemento de un [`CollectionView`](xref:Xamarin.Forms.CollectionView) puede definirse estableciendo la `ItemTemplate` propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obtener más información, vea [definir la apariencia](populate-data.md#define-item-appearance)de los elementos. |
| Celdas | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)no tiene concepto de celdas. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos de la lista. |
| Separadores de filas | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)no incluye separadores integrados. Se pueden proporcionar, si se desea, en la plantilla de elemento. |
| Selección | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)admite la selección única y múltiple. Para obtener más información, consulte [selección de CollectionView de Xamarin. Forms](selection.md). |
| Alto de fila | `HasUnevenRows`, `RowHeight` | En, el alto de la fila de cada elemento viene determinado por `ItemSizingStrategy` la propiedad. `CollectionView` Para obtener más información, vea tamaño de los [elementos](layout.md#item-sizing).|
| Almacenamiento en memoria caché | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)utiliza automáticamente la virtualización proporcionada por los controles nativos subyacentes. |
| Encabezados y pies de página | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)puede presentar un encabezado y un pie de página que se desplacen con los elementos de `Header`la lista `HeaderTemplate`, a `FooterTemplate` través de las propiedades, `Footer`, y. Para obtener más información, vea [encabezados y pies de página](layout.md#headers-and-footers). |
| Agrupar | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)muestra datos agrupados correctamente estableciendo su `IsGrouped` propiedad en. `true` Los encabezados de grupo y los pies de grupo se pueden personalizar estableciendo `GroupHeaderTemplate` las `GroupFooterTemplate` propiedades y [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en objetos. Para obtener más información, vea GROUPING de la agrupación de datos de [Xamarin. Forms](grouping.md). |
| Extraer para actualizar | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | La incorporación de cambios a la actualización no se `CollectionView`admite actualmente en, pero se agregará en una versión futura. |
| Acciones de contexto | `ContextActions` | Las acciones de contexto no se admiten `CollectionView`actualmente en, pero se agregarán en una versión futura. |
| Desplazarse | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)define `ScrollTo` métodos que desplazan los elementos en la vista. Para obtener más información, vea [desplazamiento](scrolling.md). |

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
