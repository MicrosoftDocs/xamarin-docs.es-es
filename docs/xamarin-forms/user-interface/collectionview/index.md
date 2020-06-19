---
title: CollectionView de Xamarin.Forms
description: La CollectionView es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a2c9fd9e6e48192bc2237d6b451b533fcee6e6ed
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136453"
---
# <a name="xamarinforms-collectionview"></a>CollectionView de Xamarin.Forms

## <a name="introduction"></a>[Introducción](introduction.md)

La [`CollectionView`](xref:Xamarin.Forms.CollectionView) es una vista flexible y eficaz para presentar listas de datos con diferentes especificaciones de diseño.

## <a name="data"></a>[Data](populate-data.md)

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en cualquier colección que implementa `IEnumerable` . La apariencia de cada elemento de la lista se puede definir estableciendo la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Diseño](layout.md)

De forma predeterminada, un mostrará [`CollectionView`](xref:Xamarin.Forms.CollectionView) sus elementos en una lista vertical. Sin embargo, se pueden especificar listas y cuadrículas verticales y horizontales.

## <a name="selection"></a>[Selección](selection.md)

De forma predeterminada, la [`CollectionView`](xref:Xamarin.Forms.CollectionView) selección está deshabilitada. Sin embargo, se puede habilitar la selección única y múltiple.

## <a name="empty-views"></a>[Vistas vacías](emptyview.md)

En [`CollectionView`](xref:Xamarin.Forms.CollectionView) , se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrolling"></a>[Desplazarse](scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.

## <a name="grouping"></a>[Agrupación](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede mostrar datos agrupados correctamente estableciendo su `IsGrouped` propiedad en `true` .
