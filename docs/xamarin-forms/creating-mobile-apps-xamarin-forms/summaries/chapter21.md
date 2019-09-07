---
title: Resumen del capítulo 21. Transformaciones
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 21. Transformaciones'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760576"
---
# <a name="summary-of-chapter-21-transforms"></a>Resumen del capítulo 21. Transformaciones

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Aparece una vista de Xamarin.Forms en la pantalla en una ubicación y un tamaño determinado por su elemento primario, que suele ser un `Layout` o `Layout<View>` derivado. El *transformar* es una característica de Xamarin.Forms que puede modificar esa ubicación, tamaño o incluso orientación.

Xamarin.Forms es compatible con tres tipos básicos de las transformaciones:

- *Traducción* &mdash; desplazar un elemento horizontal o verticalmente
- *Escala* &mdash; cambiar el tamaño de un elemento
- *Rotación* &mdash; activar un elemento en torno a un punto o eje

En Xamarin.Forms, el escalado es isótropo; afecta a la anchura y altura uniformemente. Se admite la rotación tanto en la superficie bidimensional de la pantalla y en el espacio 3D. No hay ninguna transformación de sesgo (o enorme) y ninguna transformación generalizada de matriz.

Se admiten las transformaciones con ocho propiedades de tipo `double` definido por el `VisualElement` clase:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Todas estas propiedades están respaldadas por propiedades enlazables. Pueden ser destinos de enlace de datos y un estilo. [**Capítulo 22. Animación** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) muestra cómo se pueden animar estas propiedades, pero algunos ejemplos de este capítulo muestran cómo animar mediante Xamarin.Forms [temporizador](~/xamarin-forms/platform/device.md#devicestarttimer).

Transformar las propiedades afectan al sólo cómo se representa el elemento y hacer *no* afectan a cómo se percibe el elemento de diseño.

## <a name="the-translation-transform"></a>La transformación de traslación

Valores distintos de cero de la [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propiedades desplazar un elemento horizontal o verticalmente.

El [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programa le permite experimentar con estas propiedades con dos `Slider` elementos que controlan la `TranslationX` y `TranslationY` las propiedades de un `Frame`. La transformación también afecta a todos los elementos secundarios de ese `Frame`.

### <a name="text-effects"></a>Efectos de texto

Un uso habitual de las propiedades de la traducción es se va a desplazar levemente la representación de texto. Esto se muestra en el [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) ejemplo:

[![Captura de pantalla triple de desplazamientos de texto](images/ch21fg03-small.png "texto desplazamientos")](images/ch21fg03-large.png#lightbox "desplazamientos de texto")

Otro efecto es representar varias copias de un `Label` similar a un bloque de 3D, como se muestra en el [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) ejemplo.

### <a name="jumps-and-animations"></a>Saltos y animaciones

El [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) ejemplo usa la traducción de mover un `Button` cada vez que se pulsa, pero el propósito principal es demostrar que el `Button` recibe la entrada del usuario en la ubicación donde representa el botón.

El [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) ejemplo es similar, pero usa un temporizador para animar el `Button` desde un punto a otro.

## <a name="the-scale-transform"></a>La transformación de escala

El [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) transformación puede aumentar o disminuir el tamaño representado del elemento. El valor predeterminado es 1. Un valor de 0 hace que el elemento sea invisible. Los valores negativos, hacer que el elemento aparezca va a girar 180 grados. El `Scale` propiedad no afecta a la `Width` o `Height` propiedades del elemento. Estos valores son iguales.

Puede experimentar con la `Scale` propiedad utilizando el [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) ejemplo.

El [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) muestra la diferencia entre animar el `Scale` propiedad de un `Button` y animar el `FontSize` propiedad. El `FontSize` propiedad afecta a cómo el `Button` se percibe en diseño; el `Scale` propiedad no.

El [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) ejemplo calcula un `Scale` propiedad que se aplica a un `Label` elemento para que sea tan grande como sea posible mientras todavía se ajusten dentro de la página.

### <a name="anchoring-the-scale"></a>Delimitación de la escala

Los elementos que se reducen horizontalmente en los tres ejemplos anteriores han todos aumenta o disminuye de tamaño en relación con el centro del elemento. En otras palabras, el elemento aumenta o disminuye de tamaño de la misma en todas las direcciones. Solo el punto en el centro del elemento permanece en la misma ubicación durante el ajuste de escala.

Puede cambiar el centro de la escala estableciendo el [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) y [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propiedades. Estas propiedades son relativas del propio elemento. Para `AnchorX`, un valor de 0 hace referencia a la izquierda del elemento y 1 hace referencia a la derecha. De forma similar para `AnchorY`, 0 es la parte superior y 1 es la parte inferior. Ambas propiedades tienen valores predeterminados de 0,5, que es el centro.

El [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) ejemplo le permite experimentar con la `AnchorX` y `AnchorY` propiedades, así como la `Scale` propiedad.

En iOS, utilizando los valores predeterminados que no sean de `AnchorX` y `AnchorY` propiedades es suelen ser incompatibles con los cambios de orientación del teléfono.

## <a name="the-rotation-transform"></a>La transformación de giro

El [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad se especifica en grados e indica un giro hacia la derecha en torno a un punto del elemento definido por `AnchorX` y `AnchorY`. El [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) le permite experimentar con estas tres propiedades.

### <a name="rotated-text-effects"></a>Efectos de texto girado

El [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) muestra las matemáticas necesarias dibujar un círculo con 64 diminutos girado `BoxView` elementos.

El [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) ejemplo muestra varias `Label` girarán elementos con la misma cadena de texto para que aparezca como radios.

El [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) ejemplo muestra una cadena de texto que aparece se ajustan en un círculo.

### <a name="an-analog-clock"></a>Un reloj analógico

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) clase que calcula los ángulos de manos de un reloj. Para evitar las dependencias de plataforma en ViewModel, la clase utiliza `Task.Delay` en lugar de un temporizador para buscar un nuevo `DateTime` valor.

También se incluye en **Xamarin.FormsBook.Toolkit** es un [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) clase que implementa `IValueConverter` y sirve para redondear un ángulo segundo a segundo más cercano.

El [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa tres girar `BoxView` elementos que se va a dibujar un reloj analógico.

El [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) usa `BoxView` para gráficos más amplios, incluidos la graduación marca alrededor de la cara del reloj y los entrega decir girar una distancia pequeña desde sus extremos:

[![Captura de pantalla triple de reloj BoxView](images/ch21fg17-small.png "cara del reloj analógico")](images/ch21fg17-large.png#lightbox "cara del reloj analógico")

Además un [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) clase **Xamarin.FormsBook.Toolkit** hace que el segundero aparezca retirar un poco antes de adelantarnos y, a continuación, para mover de nuevo en su posición correcta.

### <a name="vertical-sliders"></a>¿Controles deslizantes verticales?

El [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) ejemplo muestra que `Slider` elementos se pueden girar 90 grados y siguen funcionando. Sin embargo, resulta difícil colocar estos girado `Slider` elementos porque en el diseño aún así parecen ser horizontal.

## <a name="3d-ish-rotations"></a>Rotaciones abordaré 3D

El [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) propiedad aparece girar un elemento alrededor de un eje x 3D para que parecen mover hacia o desde el Visor de la parte superior e inferior del elemento. De forma similar, el [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) parece girar un elemento alrededor del eje y para que parezca que los lados izquierdo y derecho del elemento tiene mover hacia o desde el Visor.

El `AnchorX` afecta a la propiedad `RotationY` pero no `RotationX`. El `AnchorY` afecta a la propiedad `RotationX` pero no `RotationY`. Puede experimentar con la [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) ejemplo para explorar las interacciones de estas propiedades.

El sistema de coordenadas 3D implicado en Xamarin.Forms es zurdo. Si señala el índice de la mano izquierda en la dirección de X creciente coordina (a la derecha) y coordina el dedo intermedio en la dirección de Y creciente (abajo), a continuación, los puntos de control de posición en la dirección de aumentar las coordenadas Z (fuera de la pantalla).

Además, para cualquiera de los tres ejes, si señala el pulgar izquierdo en la dirección de aumentar los valores, a continuación, la curva de los dedos indica la dirección de giro de los ángulos de rotación positivos.

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 21 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Capítulo 21 ejemplos](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
