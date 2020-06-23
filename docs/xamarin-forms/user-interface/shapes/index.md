---
title: Xamarin.FormsColocar
description: Xamarin.FormsLas formas son tipos de vistas que permiten dibujar formas en la pantalla.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 13c79d7597325a3bf8dbabfa2983d55a92309c4b
ms.sourcegitcommit: dc49ba58510eeb52048a866e5d3daf5f1f68fbd2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130887"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsColocar

![](~/media/shared/preview.png "This API is currently pre-release")

Un `Shape` es un tipo de [`View`](xref:Xamarin.Forms.View) que permite dibujar una forma en la pantalla. `Shape`los objetos se pueden usar dentro de las clases de diseño y la mayoría de los controles, ya que la `Shape` clase se deriva de la `View` clase.

Xamarin.FormsLas formas están disponibles en el `Xamarin.Forms.Shapes` espacio de nombres de iOS, Android, MacOS, el plataforma universal de Windows (UWP) y el Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsLas formas actualmente son experimentales y solo se pueden usar si se establece la `Shapes_Experimental` marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).

`Shape` define las siguientes propiedades:

- `Aspect`, de tipo `Stretch` , describe cómo la forma rellena su espacio asignado. El valor predeterminado de esta propiedad es `Stretch.None`.
- `Fill`, de tipo [`Color`](xref:Xamarin.Forms.Color) , indica el color usado para dibujar el interior de la forma.
- `Stroke`, de tipo [`Color`](xref:Xamarin.Forms.Color) , indica el color usado para dibujar el contorno de la forma.
- `StrokeDashArray`, de tipo `DoubleCollection` , que representa una colección de `double` valores que indican el modelo de guiones y espacios que se usan para esquematizar una forma.
- `StrokeDashOffset`, de tipo `double` , especifica la distancia dentro del modelo de guiones donde comienza un guión. El valor predeterminado de esta propiedad es 0,0.
- `StrokeLineCap`, de tipo `PenLineCap` , describe la forma al principio y al final de una línea o segmento. El valor predeterminado de esta propiedad es `PenLineCap.Flat`.
- `StrokeLineJoin`, de tipo `PenLineJoin` , especifica el tipo de combinación que se usa en los vértices de una forma. El valor predeterminado de esta propiedad es `PenLineJoin.Miter`.
- `StrokeThickness`, de tipo `double` , indica el ancho del contorno de la forma. El valor predeterminado de esta propiedad es 1,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Xamarin.Formsdefine un número de objetos que derivan de la `Shape` clase. Son `Ellipse` ,, `Line` `Path` , `Polygon` , `Polyline` y `Rectangle` .

## <a name="paint-shapes"></a>Formas de dibujo

[`Color`](xref:Xamarin.Forms.Color)los objetos se utilizan para pintar las formas `Stroke` y `Fill` :

```xaml
<Ellipse Fill="DarkBlue"
         Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

En este ejemplo, se especifican el trazo y el relleno de un `Ellipse` :

![Formas de dibujo](images/ellipse.png "Formas de dibujo")

> [!IMPORTANT]
> Si no se especifica un [`Color`](xref:Xamarin.Forms.Color) valor para `Stroke` , o si se establece `StrokeThickness` en 0, no se dibujará el borde alrededor de la forma.

Para obtener más información sobre [`Color`](xref:Xamarin.Forms.Color) los valores válidos, vea [colores Xamarin.Forms en ](~/xamarin-forms/user-interface/colors.md).

## <a name="stretch-shapes"></a>Formas de ajuste

`Shape`los objetos tienen una `Aspect` propiedad, de tipo `Stretch` . Esta propiedad determina cómo `Shape` se ajusta el contenido de un objeto para rellenar el `Shape` espacio de diseño del objeto. El `Shape` espacio de diseño de un objeto es la cantidad de espacio que el `Shape` sistema de diseño asigna a Xamarin.Forms , debido a una `WidthRequest` configuración explícita y a la `HeightRequest` `HorizontalOptions` configuración de y `VerticalOptions` .

La enumeración `Stretch` define los miembros siguientes:

- `None`, que indica que el contenido conserva su tamaño original. Este es el valor predeterminado de la propiedad `Shape.Aspect`.
- `Fill`, que indica que el contenido cambia de tamaño para rellenar las dimensiones de destino. No se mantiene la relación de aspecto.
- `Uniform`, que indica que se cambia el tamaño del contenido para ajustarse a las dimensiones de destino, a la vez que se conserva la relación de aspecto.
- `UniformToFill`, indica que se cambia el tamaño del contenido para rellenar las dimensiones de destino, a la vez que se conserva la relación de aspecto. Si la relación de aspecto del rectángulo de destino no coincide con el de origen, el contenido de origen se recorta para ajustarse a las dimensiones de destino.

En el siguiente código XAML se muestra cómo establecer la `Aspect` propiedad:

```xaml
<Path Aspect="Uniform"
      Stroke="Yellow"
      StrokeThickness="1"
      Fill="Red"
      BackgroundColor="LightGray"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
    <Path.Data>
        <!-- Path data goes here -->
    </Path.Data>  
</Path>      
```

En este ejemplo, un `Path` objeto dibuja un corazón. Las `Path` `WidthRequest` propiedades y del objeto `HeightRequest` se establecen en unidades independientes del dispositivo 100 y su `Aspect` propiedad se establece en `Uniform` . Como resultado, se cambia el tamaño del contenido del objeto para ajustarse a las dimensiones de destino, a la vez que se conserva la relación de aspecto:

![Formas de ajuste](images/aspect.png "Formas de ajuste")

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Colores enXamarin.Forms](~/xamarin-forms/user-interface/colors.md)
