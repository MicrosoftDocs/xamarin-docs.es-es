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
ms.openlocfilehash: 142deccbcd29548e2d72b7144a01322f9909d98f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101276"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsFormas: elipse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

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

- [ShapeDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsColocar](index.md)