---
title: Introducción a Xamarin. Forms CarouselView
description: CarouselView es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 2fe4d984f36880493a9a04d99b63876551366477
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696968"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introducción a Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView) es una vista para presentar los datos en un diseño desplazable, donde los usuarios pueden deslizarse para desplazarse por una colección de elementos. De forma predeterminada, `CarouselView` mostrará sus elementos en una orientación horizontal. Se mostrará un solo elemento en la pantalla, con gestos de deslizar rápidamente, lo que da como resultado la navegación hacia delante y hacia atrás a través de la colección de elementos.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) está disponible en Xamarin. forms 4,3. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la clase `AppDelegate` en iOS, o bien a la clase `MainActivity` en Android, antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) está disponible en iOS y Android, pero es posible que algunas funciones solo estén disponibles parcialmente en el plataforma universal de Windows.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) comparte gran parte de su implementación con [`CollectionView`](xref:Xamarin.Forms.CollectionView). Sin embargo, los dos controles tienen diferentes casos de uso. Normalmente, `CollectionView` se usa para presentar listas de datos, de cualquier longitud, mientras que `CarouselView` se suele usar para resaltar información en una lista de longitud limitada.
