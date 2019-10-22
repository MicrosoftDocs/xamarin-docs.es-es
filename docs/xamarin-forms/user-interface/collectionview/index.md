---
title: CollectionView de Xamarin. Forms
description: La CollectionView es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8050d952556ce0b55a7ce72bc5f25de903fee6e5
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696998"
---
# <a name="xamarinforms-collectionview"></a>CollectionView de Xamarin. Forms

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

El [`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) en cualquier colección que implementa `IEnumerable`. La apariencia de cada elemento de la lista se puede definir estableciendo la propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Diseño](layout.md)

De forma predeterminada, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar listas y cuadrículas verticales y horizontales.

## <a name="selectionselectionmd"></a>[Selección](selection.md)

De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) selección está deshabilitada. Sin embargo, se puede habilitar la selección única y múltiple.

## <a name="empty-viewsemptyviewmd"></a>[Vistas vacías](emptyview.md)

En [`CollectionView`](xref:Xamarin.Forms.CollectionView), se puede especificar una vista vacía que proporciona información al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrollingscrollingmd"></a>[Desplazarse](scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define dos métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.

## <a name="groupinggroupingmd"></a>[Agrupación](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar los datos agrupados correctamente estableciendo su propiedad `IsGrouped` en `true`.
