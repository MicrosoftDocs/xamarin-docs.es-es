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
ms.openlocfilehash: 0796ba2a3b1ab28370fd8280e12df273223b9f4d
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917735"
---
# <a name="no-locxamarinforms-shapes-ellipse"></a>Xamarin.FormsFormas: elipse

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Ellipse` clase se deriva de la `Shape` clase y se puede usar para dibujar elipses y círculos. Para obtener información sobre las propiedades que la `Ellipse` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

La `Ellipse` clase establece la `Aspect` propiedad, heredada de la `Shape` clase, en `Stretch.Fill` . Para obtener más información sobre la `Aspect` propiedad, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Creación de una elipse

Para dibujar una elipse, cree un `Ellipse` objeto y establezca `WidthRequest` sus `HeightRequest` propiedades y. Para pintar el interior de la elipse, establezca su `Fill` propiedad en [`Color`](xref:Xamarin.Forms.Color) . Para asignar a la elipse un contorno, establezca su `Stroke` propiedad en [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propiedad especifica el grosor del contorno de la elipse.

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

![Circle](ellipse-images/circle.png "Circle")

Para obtener información sobre cómo dibujar una elipse discontinua, vea [dibujar formas con guiones](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
