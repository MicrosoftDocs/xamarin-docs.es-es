---
title: Xamarin.FormsCarouselView
description: CarouselView es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2ff88431869c4a14919fb13e5059cd11fcdbdc3c
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918644"
---
# <a name="no-locxamarinforms-carouselview"></a>Xamarin.FormsCarouselView

![API de versión preliminar](~/media/shared/preview.png)

## <a name="introduction"></a>[Introducción](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)Es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.

## <a name="data"></a>[Data](populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en cualquier colección que implementa `IEnumerable` . La apariencia de cada elemento se puede definir estableciendo la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Diseño](layout.md)

De forma predeterminada, un mostrará [`CarouselView`](xref:Xamarin.Forms.CarouselView) sus elementos en una lista horizontal. Sin embargo, también tiene acceso a los mismos diseños como CollectionView, incluida una orientación vertical.

## <a name="interaction"></a>[Interacción](interaction.md)

Se puede tener acceso al elemento mostrado actualmente en un [`CarouselView`](xref:Xamarin.Forms.CarouselView) a través de las `CurrentItem` `Position` propiedades y.

## <a name="empty-views"></a>[Vistas vacías](emptyview.md)

En [`CarouselView`](xref:Xamarin.Forms.CarouselView) , se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrolling"></a>[Desplazarse](scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.
