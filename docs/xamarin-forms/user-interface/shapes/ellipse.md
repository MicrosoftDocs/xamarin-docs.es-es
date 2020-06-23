---
title: 'Xamarin.FormsFormas: elipse'
description: La Xamarin.Forms clase Ellipse se puede usar para dibujar elipses y círculos.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 053805c14f195ef0dd3ae8f0cfcac2ee7425271d
ms.sourcegitcommit: dc49ba58510eeb52048a866e5d3daf5f1f68fbd2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130922"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsFormas: elipse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Ellipse` clase se deriva de la `Shape` clase y se puede usar para dibujar elipses y círculos. Para obtener información sobre las propiedades que la `Ellipse` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

La `Ellipse` clase establece la `Aspect` propiedad, heredada de la `Shape` clase, en `Stretch.Fill` . Para obtener más información sobre la `Aspect` propiedad, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Creación de una elipse

Para dibujar una elipse, cree un `Ellipse` objeto y establezca `WidthRequest` sus `HeightRequest` propiedades y. Utilice la `Fill` propiedad para especificar el [`Color`](xref:Xamarin.Forms.Color) que se usa para pintar el interior de la elipse. Utilice la `Stroke` propiedad para especificar el `Color` que se usa para pintar el contorno de la elipse. La `StrokeThickness` propiedad especifica el grosor del contorno de la elipse.

Para dibujar un círculo, haga que `WidthRequest` las `HeightRequest` propiedades y del `Ellipse` objeto sean iguales.

En el siguiente ejemplo de XAML se muestra cómo dibujar una elipse rellena:

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

En este ejemplo, se dibuja una elipse rellena roja con las dimensiones 150x50 (unidades independientes del dispositivo):

![Elipse rellena](ellipse-images/filled.png "Elipse rellena")

En el siguiente ejemplo de XAML se muestra cómo dibujar un círculo:

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

En este ejemplo, se dibuja un círculo rojo con dimensiones 150 x 150 (unidades independientes del dispositivo):

![Círculo](ellipse-images/circle.png "Circle")

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsColocar](index.md)
