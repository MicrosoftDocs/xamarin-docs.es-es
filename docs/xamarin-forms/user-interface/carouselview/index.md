---
title: CarouselView de Xamarin. Forms
description: CarouselView es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 816c1b6e4ab497d0ada0f80fa3ad4800912587c3
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696987"
---
# <a name="xamarinforms-carouselview"></a>CarouselView de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

El [`CarouselView`](xref:Xamarin.Forms.CarouselView) es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se rellena con datos estableciendo su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) en cualquier colección que implementa `IEnumerable`. La apariencia de cada elemento se puede definir estableciendo la propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Diseño](layout.md)

De forma predeterminada, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) mostrará sus elementos en una lista horizontal. Sin embargo, también tiene acceso a los mismos diseños como CollectionView, incluida una orientación vertical.

## <a name="interactioninteractionmd"></a>[Interactúe](interaction.md)

Se puede tener acceso al elemento mostrado actualmente en un [`CarouselView`](xref:Xamarin.Forms.CarouselView) a través de las propiedades `CurrentItem` y `Position`.

## <a name="empty-viewsemptyviewmd"></a>[Vistas vacías](emptyview.md)

En [`CarouselView`](xref:Xamarin.Forms.CarouselView), se puede especificar una vista vacía que proporciona información al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrollingscrollingmd"></a>[Desplazarse](scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define dos métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.
