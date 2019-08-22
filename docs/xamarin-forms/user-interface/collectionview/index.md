---
title: CollectionView de Xamarin. Forms
description: La CollectionView es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: b3841ed1287c980212ce37078f38f4984393c414
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888592"
---
# <a name="xamarinforms-collectionview"></a>CollectionView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

La [`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.

## <a name="datapopulate-datamd"></a>[Datos](populate-data.md)

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en `IEnumerable`cualquier colección que implementa. La apariencia de cada elemento de la lista se puede definir estableciendo la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)en.

## <a name="layoutlayoutmd"></a>[Diseño](layout.md)

De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) un mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar listas y cuadrículas verticales y horizontales.

## <a name="selectionselectionmd"></a>[Selección](selection.md)

De forma predeterminada [`CollectionView`](xref:Xamarin.Forms.CollectionView) , la selección está deshabilitada. Sin embargo, se puede habilitar la selección única y múltiple.

## <a name="empty-viewsemptyviewmd"></a>[Vistas vacías](emptyview.md)

En [`CollectionView`](xref:Xamarin.Forms.CollectionView), se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrollingscrollingmd"></a>[Desplazarse](scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.

## <a name="groupinggroupingmd"></a>[Agrupación](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede mostrar datos agrupados correctamente estableciendo su `IsGrouped` propiedad en. `true`
