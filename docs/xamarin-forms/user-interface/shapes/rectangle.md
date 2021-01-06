---
title: 'Xamarin.Forms Formas: rectángulo'
description: La Xamarin.Forms clase Rectangle se puede usar para dibujar rectángulos.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d70d850f0c51abfc75d15d6cefd6dc429de8c8a
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939971"
---
# <a name="no-locxamarinforms-shapes-rectangle"></a>Xamarin.Forms Formas: rectángulo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Rectangle` clase se deriva de la `Shape` clase y se puede usar para dibujar rectángulos y cuadrados. Para obtener información sobre las propiedades que la `Rectangle` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Rectangle` define las siguientes propiedades:

- `RadiusX`, de tipo `double` , que es el radio del eje x que se usa para redondear las esquinas del rectángulo. El valor predeterminado de esta propiedad es 0,0.
- `RadiusY`, de tipo `double` , que es el radio del eje y que se usa para redondear las esquinas del rectángulo. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

La `Rectangle` clase establece la `Aspect` propiedad, heredada de la `Shape` clase, en `Stretch.Fill` . Para obtener más información sobre la `Aspect` propiedad, consulte [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-a-rectangle"></a>Crear un rectángulo

Para dibujar un rectángulo, cree un `Rectangle` objeto y establezca sus `WidthRequest` `HeightRequest` propiedades y. Para pintar el interior del rectángulo, establezca su `Fill` propiedad en [`Color`](xref:Xamarin.Forms.Color) . Para dar al rectángulo un contorno, establezca su `Stroke` propiedad en [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propiedad especifica el grosor del contorno del rectángulo.

Para dar al rectángulo esquinas redondeadas, establezca sus `RadiusX` `RadiusY` propiedades y. Estas propiedades establecen los radios de los ejes x e y que se usan para redondear las esquinas del rectángulo.

Para dibujar un cuadrado, haga que `WidthRequest` las `HeightRequest` propiedades y del `Rectangle` objeto sean iguales.

En el siguiente ejemplo de XAML se muestra cómo dibujar un rectángulo relleno:

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

En este ejemplo, se dibuja un rectángulo relleno rojo con las dimensiones 150x50 (unidades independientes del dispositivo):

![Rectángulo relleno](rectangle-images/filled.png "Rectángulo relleno")

En el siguiente ejemplo de XAML se muestra cómo dibujar un rectángulo relleno con esquinas redondeadas:

```xaml
<Rectangle Fill="Blue"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10"
           WidthRequest="200"
           HeightRequest="100"
           HorizontalOptions="Start" />
```

En este ejemplo, se dibuja un rectángulo relleno azul con esquinas redondeadas:

![Rectángulo con esquinas redondeadas](rectangle-images/rounded.png "Rectángulo con esquinas redondeadas")

Para obtener información sobre cómo dibujar un rectángulo discontinuo, vea [dibujar formas con guiones](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Colocar](index.md)
