---
title: 'Xamarin.FormsPinceles: degradados lineales'
description: La Xamarin.Forms clase LinearGradientBrush pinta un área con un degradado lineal.
ms.prod: xamarin
ms.assetid: BEA2B3F5-96B0-4E39-88A6-0FAFE95C3DCD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e83e3cc110e2ab5f9bf6a1cda54fbb88e65bee17
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919152"
---
# <a name="no-locxamarinforms-brushes-linear-gradients"></a>Xamarin.FormsPinceles: degradados lineales

![API de vista previa](~/media/shared/preview.png "Esta API se encuentra en versión preliminar.")

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

La `LinearGradientBrush` clase se deriva de la `GradientBrush` clase y pinta un área con un degradado lineal, que combina dos o más colores a lo largo de una línea conocida como el eje de degradado. `GradientStop`los objetos se utilizan para especificar los colores del degradado y sus posiciones. Para obtener más información sobre los `GradientStop` objetos, consulte [ Xamarin.Forms brushes: gradientes](gradient.md).

La clase `LinearGradientBrush` define las propiedades siguientes:

- `StartPoint`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa las coordenadas bidimensionales iniciales del degradado lineal. El valor predeterminado de esta propiedad es (0,0).
- `EndPoint`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa las coordenadas bidimensionales finales del degradado lineal. El valor predeterminado de esta propiedad es (1,1).

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La `LinearGradientBrush` clase también como un `IsEmpty` método que devuelve un `bool` que representa si se ha asignado algún objeto al pincel `GradientStop` .

> [!NOTE]
> Los degradados lineales también se pueden crear con la `linear-gradient()` función CSS.

## <a name="create-a-lineargradientbrush"></a>Crear un LinearGradientBrush

Los delimitadores de degradado de un pincel de degradado lineal se colocan en el eje de degradado. La orientación y el tamaño del eje de degradado se pueden cambiar utilizando las `StartPoint` propiedades y del pincel `EndPoint` . Al manipular estas propiedades, puede crear degradados horizontales, verticales y diagonales, invertir la dirección del degradado, condensar la distribución del degradado, etc.

Las `StartPoint` `EndPoint` propiedades y son relativas al área que se está pintando. (0,0) representa la esquina superior izquierda del área que se está dibujando y (1,1) representa la esquina inferior derecha del área que se está pintando. En el diagrama siguiente se muestra el eje de degradado de un pincel de degradado lineal diagonal:

![Marco con un eje de degradado diagonal](lineargradient-images/gradient-axis.png)

En este diagrama, la línea discontinua muestra el eje de degradado, que resalta la ruta de interpolación del degradado desde el punto inicial hasta el punto final.

### <a name="create-a-horizontal-linear-gradient"></a>Crear un degradado lineal horizontal

Para crear un degradado lineal horizontal, cree un `LinearGradientBrush` objeto y establezca su `StartPoint` en (0,0) y su `EndPoint` en (1,0). A continuación, agregue dos o más `GradientStop` objetos a la `LinearGradientBrush.GradientStops` colección, que especifican los colores del degradado y sus posiciones.

En el siguiente ejemplo de XAML se muestra un `LinearGradientBrush` valor horizontal que se establece como `Background` de un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->
        <LinearGradientBrush EndPoint="1,0">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>  
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `LinearGradientBrush` que se interpola de amarillo a verde horizontal:

![Marco pintado con un LinearGradientBrush horizontal](lineargradient-images/horizontal.png)

### <a name="create-a-vertical-linear-gradient"></a>Crear un degradado lineal vertical

Para crear un degradado lineal vertical, cree un `LinearGradientBrush` objeto y establezca su `StartPoint` en (0,0) y su `EndPoint` en (0, 1). A continuación, agregue dos o más `GradientStop` objetos a la `LinearGradientBrush.GradientStops` colección, que especifican los colores del degradado y sus posiciones.

En el siguiente ejemplo de XAML se muestra un `LinearGradientBrush` valor vertical que se establece como `Background` de un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->    
        <LinearGradientBrush EndPoint="0,1">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `LinearGradientBrush` que se interpola de amarillo a verde verticalmente:

![Marco pintado con un LinearGradientBrush vertical](lineargradient-images/vertical.png)

### <a name="create-a-diagonal-linear-gradient"></a>Crear un degradado lineal diagonal

Para crear un degradado lineal diagonal, cree un `LinearGradientBrush` objeto y establezca su `StartPoint` en (0,0) y su `EndPoint` en (1,1). A continuación, agregue dos o más `GradientStop` objetos a la `LinearGradientBrush.GradientStops` colección, que especifican los colores del degradado y sus posiciones.

En el siguiente ejemplo de XAML se muestra una diagonal `LinearGradientBrush` que se establece como la `Background` de un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0)      
             Endpoint defaults to (1,1) -->
        <LinearGradientBrush>
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

En este ejemplo, el fondo de [`Frame`](xref:Xamarin.Forms.Frame) se pinta con un `LinearGradientBrush` que se interpola de amarillo a verde en diagonal:

![Marco pintado con un LinearGradientBrush diagonal](lineargradient-images/diagonal.png)

## <a name="related-links"></a>Vínculos relacionados

- [BrushesDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.FormsPinceles: degradados](gradient.md)
