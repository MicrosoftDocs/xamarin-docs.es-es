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
ms.openlocfilehash: 1a1ba09b5be6901b1b07ed9aa3fd9a1b88770efc
ms.sourcegitcommit: 91b4d2f93687fadec5c3f80aadc8f7298d911624
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2020
ms.locfileid: "85795017"
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

## <a name="draw-dashed-shapes"></a>Dibujar formas discontinuas

`Shape`los objetos tienen una `StrokeDashArray` propiedad, de tipo `DoubleCollection` . Esta propiedad representa una colección de `double` valores que indican el modelo de guiones y espacios que se usan para esquematizar una forma. Un `DoubleCollection` es `ObservableCollection` de `double` valores. Cada `double` de la colección especifica la longitud de un guión o un espacio. El primer elemento de la colección, que se encuentra en el índice 0, especifica la longitud de un guión. El segundo elemento de la colección, que se encuentra en el índice 1, especifica la longitud de un intervalo. Por lo tanto, los objetos con un valor de índice par especifican guiones, mientras que los objetos con un valor de índice impar especifican huecos.

`Shape`los objetos también tienen una `StrokeDashOffset` propiedad, de tipo `double` , que especifica la distancia dentro del patrón de guiones donde comienza un guión. Si no se establece esta propiedad, se producirá `Shape` un contorno sólido.

Las formas de guiones se pueden dibujar mediante el establecimiento de las `StrokeDashArray` `StrokeDashOffset` propiedades y. La `StrokeDashArray` propiedad se debe establecer en uno o más `double` valores, donde cada par está delimitado por una sola coma y/o uno o varios espacios. Por ejemplo, "0,5 1,0" y "0.5, 1.0" son válidos.

En el siguiente ejemplo de XAML se muestra cómo dibujar un rectángulo discontinuo:

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           StrokeDashArray="1,1"
           StrokeDashOffset="6"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

En este ejemplo, se dibuja un rectángulo relleno con un trazo discontinuo:

![Rectángulo discontinuo](images/dashed-rectangle.png "Línea discontinua")

## <a name="control-line-ends"></a>Extremos de la línea de control

Una línea tiene tres partes: el extremo inicial, el cuerpo de la línea y el extremo final. Los extremos inicial y final describen la forma al principio y al final de una línea, o segmento.

`Shape`los objetos tienen una `StrokeLineCap` propiedad, de tipo `PenLineCap` , que describe la forma en el principio y el final de una línea, o segmento. La enumeración `PenLineCap` define los miembros siguientes:

- `Flat`, que representa un extremo que no se extiende más allá del último punto de la línea. Esto es comparable a ningún extremo de línea y es el valor predeterminado de la `StrokeLineCap` propiedad.
- `Square`, que representa un rectángulo que tiene un alto igual al grosor de la línea y una longitud igual a la mitad del grosor de la línea.
- `Round`, que representa un semicírculo que tiene un diámetro igual al grosor de la línea.

> [!IMPORTANT]
> La `StrokeLineCap` propiedad no tiene ningún efecto si se establece en una forma que no tiene ningún punto inicial o final. Por ejemplo, esta propiedad no tiene ningún efecto si se establece en `Ellipse` , o `Rectangle` .

En el siguiente código XAML se muestra cómo establecer la `StrokeLineCap` propiedad:

```xaml
<Line X1="0"
      Y1="20"
      X2="300"
      Y2="20"
      StrokeLineCap="Round"
      Stroke="Red"
      StrokeThickness="12" />
```

En este ejemplo, la línea roja se redondea al principio y al final de la línea:

![Extremos de línea](images/linecap.png "Extremos de línea")

## <a name="control-line-joins"></a>Combinaciones de línea de control

`Shape`los objetos tienen una `StrokeLineJoin` propiedad, de tipo `PenLineJoin` , que especifica el tipo de combinación que se usa en los vértices de la forma. La enumeración `PenLineJoin` define los miembros siguientes:

- `Miter`, que representa los vértices angulares normales. Este es el valor predeterminado de la propiedad `StrokeLineJoin`.
- `Bevel`, que representa los vértices biselados.
- `Round`, que representa los vértices redondeados.

En el siguiente código XAML se muestra cómo establecer la `StrokeLineJoin` propiedad:

```xaml
<Polyline Points="20 20,250 50,20 120"
          Stroke="DarkBlue"
          StrokeThickness="20"
          StrokeLineJoin="Round" />
```

En este ejemplo, la polilínea azul oscuro tiene combinaciones redondeadas en sus vértices:

![Combinaciones de líneas](images/linejoin.png "Combinaciones de líneas")

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Colores enXamarin.Forms](~/xamarin-forms/user-interface/colors.md)
