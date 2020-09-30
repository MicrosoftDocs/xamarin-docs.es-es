---
title: 'Xamarin.Forms Pinceles: degradados radiales'
description: La Xamarin.Forms clase RadialGradientBrush pinta un área con un degradado radial.
ms.prod: xamarin
ms.assetid: 099BA530-3B38-4005-9B19-A0EB4D4DEEFC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a56d2f590b78bef0f47c764862b891c9c0d46129
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563580"
---
# <a name="no-locxamarinforms-brushes-radial-gradients"></a>Xamarin.Forms Pinceles: degradados radiales

![API de vista previa](~/media/shared/preview.png "Esta API se encuentra en versión preliminar.")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

La `RadialGradientBrush` clase se deriva de la `GradientBrush` clase y pinta un área con un degradado radial, que combina dos o más colores en un círculo. `GradientStop` los objetos se utilizan para especificar los colores del degradado y sus posiciones. Para obtener más información sobre los `GradientStop` objetos, consulte [ Xamarin.Forms brushes: gradientes](gradient.md).

La clase `RadialGradientBrush` define las propiedades siguientes:

- `Center`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto central del círculo del degradado radial. El valor predeterminado de esta propiedad es (0.5, 0.5).
- `Radius`, de tipo `double` , que representa el radio del círculo del degradado radial. El valor predeterminado de esta propiedad es 0,5.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

La `RadialGradientBrush` clase también tiene un `IsEmpty` método que devuelve un `bool` que indica si se ha asignado algún objeto al pincel `GradientStop` .

> [!NOTE]
> También se pueden crear degradados radiales con la `radial-gradient()` función CSS.

## <a name="create-a-radialgradientbrush"></a>Crear un RadialGradientBrush

Los delimitadores de degradado del pincel de degradado radial se colocan a lo largo de un eje de degradado definido por un círculo. El eje de degradado irradia desde el centro del círculo hasta su circunferencia. La posición y el tamaño del círculo se pueden cambiar utilizando las `Center` propiedades y del pincel `Radius` . El círculo define el punto final del degradado. Por lo tanto, un delimitador de degradado en 1,0 define el color de la circunferencia del círculo. Un delimitador de degradado en 0,0 define el color en el centro del círculo.

Para crear un degradado radial, cree un `RadialGradientBrush` objeto y establezca `Center` sus `Radius` propiedades y. A continuación, agregue dos o más `GradientStop` objetos a la `RadialGradientBrush.GradientStops` colección, que especifican los colores del degradado y sus posiciones.

En el siguiente ejemplo de XAML se muestra un `RadialGradientBrush` que se establece como la `Background` de un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml    
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- Center defaults to (0.5,0.5)
             Radius defaults to (0.5) -->
        <RadialGradientBrush>
            <GradientStop Color="Red"
                          Offset="0.1" />
            <GradientStop Color="DarkBlue"
                          Offset="1.0" />
        </RadialGradientBrush>
    </Frame.Background>
</Frame>
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `RadialGradientBrush` que se interpola de rojo a azul oscuro. El centro del degradado radial se coloca en el centro del `Frame` :

![Marco pintado con un RadialGradientBrush centrado](radialgradient-images/center.png)

En el siguiente ejemplo de XAML se mueve el centro del degradado radial a la esquina superior izquierda del [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="0.0,0.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `RadialGradientBrush` que se interpola de rojo a azul oscuro. El centro del degradado radial se coloca en la parte superior izquierda del `Frame` :

![Marco pintado con un RadialGradientBrush superior izquierdo](radialgradient-images/top-left.png)

En el siguiente ejemplo de XAML se mueve el centro del degradado radial a la esquina inferior derecha de [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="1.0,1.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>            
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `RadialGradientBrush` que se interpola de rojo a azul oscuro. El centro del degradado radial se coloca en la parte inferior derecha del `Frame` :

![Marco pintado con un RadialGradientBrush de la derecha inferior](radialgradient-images/bottom-right.png)

## <a name="related-links"></a>Vínculos relacionados

- [BrushesDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms Pinceles: degradados](gradient.md)