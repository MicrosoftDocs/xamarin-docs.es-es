---
title: 'Xamarin.FormsPinceles: degradados'
description: La Xamarin.Forms clase GradientBrush es una clase abstracta que describe un degradado, formado por delimitadores de degradado.
ms.prod: xamarin
ms.assetid: 24763E56-74EC-4082-897B-E4EAACCADFEE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 254a4433888be0e441ae37d41c24b1886040998b
ms.sourcegitcommit: 579ec4f2884fa391e5e214a3952cd6004c521eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919624"
---
# <a name="no-locxamarinforms-brushes-gradients"></a>Xamarin.FormsPinceles: degradados

![API de vista previa](~/media/shared/preview.png "Esta API se encuentra en versión preliminar.")

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)

La `GradientBrush` clase se deriva de la `Brush` clase y es una clase abstracta que describe un degradado, formado por delimitadores de degradado. Un pincel de degradado pinta un área con varios colores que se mezclan entre sí a lo largo de un eje. Las clases que derivan de `GradientBrush` describen diferentes formas de interpretar los delimitadores de degradado y Xamarin.Forms proporcionan los siguientes pinceles de degradado:

- `LinearGradientBrush`, que pinta un área con un degradado lineal. Para obtener más información, vea [ Xamarin.Forms pinceles: degradados lineales](lineargradient.md).
- `RadialGradientBrush`, que pinta un área con un degradado radial. Para obtener más información, vea [ Xamarin.Forms pinceles: degradados radiales](radialgradient.md).

La `GradientBrush` clase define la `GradientStops` propiedad, de tipo `GradientStopsCollection` , que representa los delimitadores de degradado del pincel, cada uno de los cuales especifica un color y un desplazamiento a lo largo del eje de degradado del pincel. Un `GradientStopsCollection` es `ObservableCollection` de `GradientStop` objetos. La `GradientStops` propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

> [!NOTE]
> La `GradientStops` propiedad es `ContentProperty` de la `GradientBrush` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

## <a name="gradient-stops"></a>Delimitadores de degradado

Los delimitadores de degradado son los bloques de creación de un pincel de degradado y especifican los colores del degradado y su ubicación a lo largo del eje de degradado. Los delimitadores de degradado se especifican mediante `GradientStop` objetos.

La clase `GradientStop` define las propiedades siguientes:

- `Color`, de tipo [`Color`](xref:Xamarin.Forms.Color) , que representa el color del delimitador de degradado. El valor predeterminado de esta propiedad es `Color.Default`.
- `Offset`, de tipo `float` , que representa la ubicación del delimitador de degradado dentro del vector de degradado. El valor predeterminado de esta propiedad es 0 y los valores válidos están en el intervalo 0.0-1.0. Cuanto más se acerque este valor a 0, más próximo será el color al inicio del degradado. Del mismo modo, cuanto más se acerque este valor a 1, más cerca será el color hasta el final del degradado.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!IMPORTANT]
> El sistema de coordenadas utilizado por los degradados es relativo a un cuadro de límite para el área de salida. 0 indica un 0 por ciento del rectángulo de selección, mientras que 1 indica un 100 por cien del rectángulo de selección. Por lo tanto, (0.5, 0.5) describe un punto en el centro del cuadro de límite y (1,1) describe un punto en la parte inferior derecha del cuadro de límite.

En el ejemplo de XAML siguiente se crea una diagonal `LinearGradientBrush` con cuatro colores:

```xaml
<LinearGradientBrush StartPoint="0,0"
                     EndPoint="1,1">
    <GradientStop Color="Yellow"
                  Offset="0.0" />
    <GradientStop Color="Red"
                  Offset="0.25" />
    <GradientStop Color="Blue"
                  Offset="0.75" />             
    <GradientStop Color="LimeGreen"
                  Offset="1.0" />
</LinearGradientBrush>                                                       
```

El color de cada punto entre delimitadores de degradado se interpola como una combinación del color especificado por los dos delimitadores de degradado de límite. En el diagrama siguiente se muestran los delimitadores de degradado del ejemplo anterior:

![Marco pintado con un LinearGradientBrush diagonal](gradient-images/gradient-stops.png)

En este diagrama, los círculos marcan la posición de los delimitadores de degradado y la línea discontinua muestra el eje de degradado. El primer delimitador de degradado especifica el color amarillo en un desplazamiento de 0,0. El segundo delimitador de degradado especifica el color rojo en un desplazamiento de 0,25. Los puntos entre estos dos delimitadores de degradado cambian gradualmente de amarillo a rojo a medida que se desplaza de izquierda a derecha a lo largo del eje de degradado. El tercer delimitador de degradado especifica el color azul en un desplazamiento de 0,75. Los puntos entre el segundo y el tercer delimitador de degradado cambian gradualmente de rojo a azul. El cuarto delimitador de degradado especifica el color verde lima en el desplazamiento de 1,0. Los puntos entre el tercer y el cuarto delimitador de degradado cambian gradualmente de azul a verde lima.

## <a name="related-links"></a>Vínculos relacionados

- [BrushesDemos (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)
- [Xamarin.FormsPinceles: degradados lineales](lineargradient.md)
- [Xamarin.FormsPinceles: degradados radiales](radialgradient.md)
