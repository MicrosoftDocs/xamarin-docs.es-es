---
title: 'Xamarin.Forms Pinceles: colores sólidos'
description: La Xamarin.Forms clase SolidColorBrush pinta un área con un color sólido.
ms.prod: xamarin
ms.assetid: 4225D40A-16C1-40E1-ACBE-23E321E7FDE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3c3caf064ca550086f8e7924786ac8bcaf1badfc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556287"
---
# <a name="no-locxamarinforms-brushes-solid-colors"></a>Xamarin.Forms Pinceles: colores sólidos

![API de vista previa](~/media/shared/preview.png "Esta API se encuentra en versión preliminar.")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

La `SolidColorBrush` clase se deriva de la `Brush` clase y se utiliza para pintar un área con un color sólido. Hay varios métodos para especificar el color de un `SolidColorBrush` . Por ejemplo, puede especificar su color con un [`Color`](xref:Xamarin.Forms.Color) valor o mediante uno de los objetos predefinidos `SolidColorBrush` proporcionados por la `Brush` clase.

La `SolidColorBrush` clase define la `Color` propiedad, de tipo [`Color`](xref:Xamarin.Forms.Color) , que representa el color del pincel. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

La `SolidColorBrush` clase también tiene un `IsEmpty` método que devuelve un `bool` que indica si se ha asignado un color al pincel.

## <a name="create-a-solidcolorbrush"></a>Crear un SolidColorBrush

Existen tres técnicas principales para crear un `SolidColorBrush` . Puede crear un a `SolidColorBrush` partir de un [`Color`](xref:Xamarin.Forms.Color) , usar un pincel predefinido o crear un `SolidColorBrush` con una notación hexadecimal.

### <a name="use-a-predefined-color"></a>Usar un color predefinido

Xamarin.Forms incluye un convertidor de tipos que crea un a `SolidColorBrush` partir de un [`Color`](xref:Xamarin.Forms.Color) valor. En XAML, esto permite `SolidColorBrush` crear una a partir de un valor predefinido `Color` :

```xaml
<Frame Background="DarkBlue"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un azul oscuro `SolidColorBrush` :

![Marco pintado con un color predefinido](solidcolor-images/predefined-color.png)

Como alternativa, [`Color`](xref:Xamarin.Forms.Color) se puede especificar el valor mediante la sintaxis de etiqueta de propiedad:

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
       <Frame.Background>
           <SolidColorBrush Color="DarkBlue" />
       </Frame.Background>
</Frame>
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `SolidColorBrush` cuyo color se especifica estableciendo la `SolidColorBrush.Color` propiedad.

### <a name="use-a-predefined-brush"></a>Usar un pincel predefinido

La `Brush` clase define un conjunto de objetos de uso común `SolidColorBrush` . En el ejemplo siguiente se usa uno de estos objetos predefinidos `SolidColorBrush` :

```xaml
<Frame Background="{x:Static Brush.Indigo}"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />       
```

El código de C# equivalente es el siguiente:

```csharp
Frame frame = new Frame
{
    Background = Brush.Indigo,
    BorderColor = Color.LightGray,
    // ...
};
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un Indigo `SolidColorBrush` :

![Marco pintado con un SolidColorBrush predefinido](solidcolor-images/predefined-brush.png)

Para obtener una lista de los objetos predefinidos `SolidColorBrush` proporcionados por la `Brush` clase, vea [pinceles de color sólido](#solid-color-brushes).

### <a name="use-hexadecimal-notation"></a>Usar notación hexadecimal

`SolidColorBrush` los objetos también se pueden crear mediante la notación hexadecimal. Con este enfoque, se especifica un color en términos de la cantidad de rojo, verde y azul que se va a combinar en un solo color. El formato principal para especificar un color mediante la notación hexadecimal es `#rrggbb` , donde:

- `rr` es un número hexadecimal de dos dígitos que especifica la cantidad relativa de rojo.
- `gg` es un número hexadecimal de dos dígitos que especifica la cantidad relativa de verde.
- `bb` es un número hexadecimal de dos dígitos que especifica la cantidad relativa de azul.

Además, se puede especificar un color como `#aarrggbb` donde `aa` especifica el valor alfa, o transparencia, del color. Este enfoque le permite crear colores que sean parcialmente transparentes.

En el ejemplo siguiente se establece el valor de color de un `SolidColorBrush` mediante una notación hexadecimal:

```xaml
<Frame Background="#FF9988"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un color de salmón `SolidColorBrush` :

![Marco pintado con un SolidColorBrush creado mediante la notación hexadecimal](solidcolor-images/hex.png)

Para otras formas de describir el color, vea [colores en Xamarin.Forms ](~/xamarin-forms/user-interface/colors.md).

## <a name="solid-color-brushes"></a>Pinceles de color sólido

Para mayor comodidad, la `Brush` clase proporciona un conjunto de objetos de uso común `SolidColorBrush` , como `AliceBlue` y `YellowGreen` . En la imagen siguiente se muestra el color de cada pincel predefinido, su nombre y su valor hexadecimal:

[![Tabla de colores que incluye una muestra de color, un nombre de color y un valor hexadecimal](solidcolor-images/solidcolorbrushes.png)](solidcolor-images/solidcolorbrushes-large.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [BrushesDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Colores en Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)