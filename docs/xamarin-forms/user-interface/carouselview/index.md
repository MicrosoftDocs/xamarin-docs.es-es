---
title: CarouselView de Xamarin. Forms
description: CarouselView es una vista para presentar listas de datos en un diseño de tipo carrusel.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 06d20341053c4f77cb72aac9e1abdc85d9f95fdb
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908335"
---
# <a name="xamarinforms-carouselview"></a>CarouselView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

> [!IMPORTANT]
> La documentación de CarouselView está en desarrollo y algunos vínculos pueden hacer referencia a la documentación de CollectionView. En la mayoría de los casos, la información debe ser aplicable debido a la naturaleza de CarouselView que se basa en CollectionView.

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) Es una vista para presentar los datos en un diseño de tipo carrusel. Su implementación se basa en la de [`CollectionView`](xref:Xamarin.Forms.CollectionView).

## <a name="datacollectionviewpopulate-datamd"></a>[Datos](../collectionview/populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en `IEnumerable`cualquier colección que implementa. La apariencia de cada elemento de la lista se puede definir estableciendo la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)en.

## <a name="layoutlayoutmd"></a>[Diseño](layout.md)

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) un mostrará sus elementos en una lista horizontal. Sin embargo, también tiene acceso a los mismos diseños como CollectionView, incluida una orientación vertical.

## <a name="empty-viewscollectionviewemptyviewmd"></a>[Vistas vacías](../collectionview/emptyview.md)

En [`CarouselView`](xref:Xamarin.Forms.CarouselView), se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para mostrar. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrollingcollectionviewscrollingmd"></a>[Desplazarse](../collectionview/scrolling.md)

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazan los elementos mediante programación a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista.
