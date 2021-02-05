---
title: 'Xamarin.Forms Formas: elipse'
description: La Xamarin.Forms clase Ellipse se puede usar para dibujar elipses y círculos.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b3130fd4cb054799ca9e0a61d13cacb2629320b7
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585837"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.Forms Formas: elipse

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Ellipse` clase se deriva de la `Shape` clase y se puede usar para dibujar elipses y círculos. Para obtener información sobre las propiedades que la `Ellipse` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

La `Ellipse` clase establece la `Aspect` propiedad, heredada de la `Shape` clase, en `Stretch.Fill` . Para obtener más información sobre la `Aspect` propiedad, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Creación de una elipse

Para dibujar una elipse, cree un `Ellipse` objeto y establezca `WidthRequest` sus `HeightRequest` propiedades y. Para pintar el interior de la elipse, establezca su `Fill` propiedad en un [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado de. Para asignar a la elipse un contorno, establezca su `Stroke` propiedad en un [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado de. La `StrokeThickness` propiedad especifica el grosor del contorno de la elipse. Para obtener más información sobre los `Brush` objetos, vea [ Xamarin.Forms pinceles](~/xamarin-forms/user-interface/brushes/index.md).

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

![Círculo no relleno](ellipse-images/circle.png "Circle")

Para obtener información sobre cómo dibujar una elipse discontinua, vea [dibujar formas con guiones](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Colocar](index.md)
- [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md)
