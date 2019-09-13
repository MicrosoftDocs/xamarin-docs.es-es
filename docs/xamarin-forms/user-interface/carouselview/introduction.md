---
title: Introducción a Xamarin. Forms CarouselView
description: CarouselView es una vista para presentar los datos en un diseño de tipo carrusel.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 244582d579780a962dfcb25c1a081d768b42acd3
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908353"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introducción a Xamarin. Forms CarouselView

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)es una vista para presentar información en un modo de carrusel.


[`CarouselView`](xref:Xamarin.Forms.CarouselView)está disponible en Xamarin. Forms 4,3. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la `AppDelegate` clase en iOS, o bien a `MainActivity` la clase en Android, antes `Forms.Init`de llamar a:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

_Nota: En el que se redactó este documento, el CarouselView todavía usa la marca CollectionView para habilitar su funcionalidad._

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)está disponible en iOS y Android, pero es posible que algunas funciones solo estén disponibles parcialmente en el Plataforma universal de Windows.

## <a name="when-to-use-carouselview"></a>Cuándo usar CarouselView

Aunque el CarouselView basa gran parte de su implementación en una de CollectionView, su finalidad es más centrada. Aunque el uso de CollectionView y CarouselView está a su discreción, los carruseles en las aplicaciones se suelen usar para resaltar información y se limitan en el número total de elementos usados.
