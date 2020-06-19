---
title: 'Xamarin.FormsFormas: elipse'
description: La Xamarin.Forms clase Ellipse se puede usar para dibujar elipses y círculos.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7638a7a9bb272d1f1904c3e446cdf0b7838c27bd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84947350"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsFormas: elipse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Ellipse` clase se deriva de la `Shape` clase y se puede usar para dibujar elipses y círculos. Para obtener información sobre las propiedades que la `Ellipse` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

La `Ellipse` clase establece la `Aspect` propiedad, heredada de la `Shape` clase, en `Stretch.Fill` .

## <a name="create-an-ellipse"></a>Creación de una elipse

En el siguiente ejemplo de XAML se muestra cómo dibujar una elipse rellena:

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

En el siguiente ejemplo de XAML se muestra cómo dibujar un círculo:

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapedemos/)
- [Xamarin.FormsColocar](index.md)
