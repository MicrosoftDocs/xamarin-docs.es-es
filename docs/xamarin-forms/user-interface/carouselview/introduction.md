---
title: Xamarin.FormsIntroducción a CarouselView
description: CarouselView es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e67acd0188e1147481005502ad9ccdaada645d9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140275"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin.FormsIntroducción a CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos. De forma predeterminada, mostrará `CarouselView` sus elementos en una orientación horizontal. Se mostrará un solo elemento en la pantalla, con gestos de deslizar rápidamente, lo que da como resultado la navegación hacia delante y hacia atrás a través de la colección de elementos. Además, se pueden mostrar indicadores que representan cada elemento de `CarouselView` :

[![Captura de pantalla de CarouselView y IndicatorView en iOS y Android](populate-data-images/indicators.png "Círculos IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos IndicatorView")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)está disponible en Xamarin.Forms 4,3. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la `AppDelegate` clase en iOS, o bien a la `MainActivity` clase en Android, antes de llamar a `Forms.Init` :

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)está disponible en iOS y Android, pero es posible que algunas funciones solo estén disponibles parcialmente en el Plataforma universal de Windows.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)comparte gran parte de su implementación con [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Sin embargo, los dos controles tienen diferentes casos de uso. `CollectionView`normalmente se usa para presentar listas de datos de cualquier longitud, mientras que `CarouselView` normalmente se usa para resaltar información en una lista de longitud limitada.
