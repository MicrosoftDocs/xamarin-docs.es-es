---
title: Resumen del capítulo 21. Transformaciones
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 21. Transformaciones'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 32393108f84ea3a57079c86b6a9a8e628ceca03a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136674"
---
# <a name="summary-of-chapter-21-transforms"></a>Resumen del capítulo 21. Transformaciones

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Una vista de Xamarin.Forms aparece en la pantalla en una ubicación y con un tamaño determinados por su elemento primario, que generalmente es un derivado de `Layout` o `Layout<View>`. La propiedad *transform* es una característica de Xamarin.Forms que puede modificar esa ubicación, tamaño o incluso orientación.

Xamarin.Forms admite tres tipos básicos de transformaciones:

- La *traslación* &mdash; desplaza un elemento horizontal o verticalmente.
- La *escala* &mdash; cambia el tamaño de un elemento.
- La *rotación* &mdash; gira un elemento alrededor de un punto o eje.

En Xamarin.Forms, el escalado es isotrópico; afecta al ancho y al alto de forma uniforme. La rotación se admite tanto en la superficie bidimensional de la pantalla como en el espacio 3D. No hay ninguna transformación de sesgo (o abundante) ni transformación de matriz generalizada.

Las transformaciones se admiten con ocho propiedades de tipo `double` definidas por la clase `VisualElement`:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Todas estas propiedades están respaldadas por propiedades enlazables. Pueden ser destinos de enlace de datos y con estilo. En el [**Capítulo 22. Animación**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) se indica cómo se pueden animar estas propiedades, pero ya en algunos ejemplos de este capítulo se muestra cómo se pueden animar mediante el [temporizador](~/xamarin-forms/platform/device.md#devicestarttimer) de Xamarin.Forms.

Las propiedades de transformación solo afectan al modo en que se representa el elemento y *no* afectan a la percepción del elemento en el diseño.

## <a name="the-translation-transform"></a>Transformación de traslación

Los valores distintos de cero de las propiedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) desplazan un elemento horizontal o verticalmente.

El programa [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) permite experimentar con estas propiedades con dos elementos `Slider` que controlan las propiedades `TranslationX` y `TranslationY` de `Frame`. La transformación también afecta a todos los elementos secundarios de ese `Frame`.

### <a name="text-effects"></a>Efectos de texto

Un uso común de las propiedades de traslación consiste en desplazar ligeramente la representación del texto. Esto se muestra en el ejemplo [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets):

[![Captura de pantalla triple de desplazamientos de texto](images/ch21fg03-small.png "Desplazamientos de texto")](images/ch21fg03-large.png#lightbox "Desplazamientos de texto")

Otro efecto consiste en representar varias copias de `Label` para que se parezca a un bloque 3D, como se muestra en el ejemplo [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText).

### <a name="jumps-and-animations"></a>Saltos y animaciones

En el ejemplo [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) se usa la traslación para trasladar `Button` cada vez que se pulse, pero el objetivo principal es demostrar que `Button` recibe la entrada del usuario en la ubicación donde se representa el botón.

El ejemplo [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) es similar, pero utiliza un temporizador para animar `Button` de un punto a otro.

## <a name="the-scale-transform"></a>Transformación de escala

La transformación de [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) puede aumentar o disminuir el tamaño representado del elemento. El valor predeterminado es 1. Un valor de 0 hace que el elemento sea invisible. Los valores negativos hacen que el elemento aparezca girado 180 grados. La propiedad `Scale` no afecta a las propiedades `Width` o `Height` del elemento. Esos valores siguen siendo los mismos.

Puede experimentar con la propiedad `Scale` mediante el ejemplo [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo).

En el ejemplo [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) se muestra la diferencia entre animar la propiedad `Scale` de `Button` y animar la propiedad `FontSize`. La propiedad `FontSize` afecta a cómo se percibe el `Button` en el diseño; la propiedad `Scale` no lo hace.

El ejemplo [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) calcula una propiedad `Scale` que se aplica a un elemento `Label` para que sea lo más grande posible mientras sigue ajustándose dentro de la página.

### <a name="anchoring-the-scale"></a>Anclaje de la escala

Los elementos escalados en los tres ejemplos anteriores tienen el tamaño aumentado o disminuido en relación con el centro del elemento. En otras palabras, el elemento aumenta o disminuye en tamaño igual en todas las direcciones. Únicamente el punto en el centro del elemento permanece en la misma ubicación durante la escala.

Puede cambiar el centro de la escala estableciendo las propiedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) y [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY). Estas propiedades son relativas al propio elemento. Para `AnchorX`, un valor de 0 hace referencia al lado izquierdo del elemento y 1 hace referencia al lado derecho. De forma similar para `AnchorY`, 0 es la parte superior y 1 es la parte inferior. Ambas propiedades tienen valores predeterminados de 0,5, que es el centro.

El ejemplo [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) permite experimentar con las propiedades `AnchorX` y `AnchorY`, así como la propiedad `Scale`.

En iOS, el uso de valores no predeterminados de las propiedades `AnchorX` y `AnchorY` es generalmente incompatible con los cambios de orientación del teléfono.

## <a name="the-rotation-transform"></a>Transformación de giro

La propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) se especifica en grados y indica el giro en el sentido de las agujas del reloj alrededor de un punto del elemento definido por `AnchorX` y `AnchorY`. [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) permite experimentar con estas tres propiedades.

### <a name="rotated-text-effects"></a>Efectos de texto girado

En el ejemplo [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) se muestran las matemáticas necesarias para dibujar un círculo con 64 elementos pequeños de `BoxView` girados.

En el ejemplo [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) se muestran varios elementos de `Label` con la misma cadena de texto girada para que aparezcan como radios.

En el ejemplo [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) se muestra una cadena de texto que aparece ajustada en un círculo.

### <a name="an-analog-clock"></a>Un reloj analógico

La biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una clase [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) que calcula los ángulos para las manecillas de un reloj. Para evitar las dependencias de plataforma en ViewModel, la clase usa `Task.Delay` en lugar de un temporizador para buscar un nuevo valor de `DateTime`.

También incluido en **Xamarin.FormsBook.Toolkit**, es una clase [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) que implementa `IValueConverter` y sirve para redondear un segundo ángulo al segundo más cercano.

En [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) se usan tres elementos `BoxView` de rotación para dibujar un reloj analógico.

En [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) se usa `BoxView` para gráficos más extensos, incluidas las marcas de graduación en torno a la superficie del reloj, y manecillas que giran una pequeña distancia desde sus extremos:

[![Captura de pantalla triple del reloj BoxView](images/ch21fg17-small.png "Superficie del reloj analógico")](images/ch21fg17-large.png#lightbox "Superficie del reloj analógico")

Además, una clase [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) en **Xamarin.FormsBook.Toolkit** hace que parezca que la segunda manecilla se retrasas un poco antes de avanzar y, a continuación, volver a la posición correcta.

### <a name="vertical-sliders"></a>¿Controles deslizante verticales?

En el ejemplo [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) se muestra que los elementos `Slider` se pueden girar 90 grados y seguir funcionando. Sin embargo, es difícil colocar estos elementos `Slider` girados porque en el diseño siguen apareciendo horizontalmente.

## <a name="3d-ish-rotations"></a>Rotaciones tridimensionales

La propiedad [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) parece girar un elemento alrededor de un eje X en 3D para que parezca que la parte superior e inferior del elemento van hacia el visor o se alejan de este. Del mismo modo, [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) parece girar un elemento alrededor del eje Y para que parezca que los lados izquierdo y derecho del elemento se acercan o se alejan del visor.

La propiedad `AnchorX` afecta a `RotationY` pero no a `RotationX`. La propiedad `AnchorY` afecta a `RotationX` pero no a `RotationY`. Puede experimentar con el ejemplo [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) para explorar las interacciones de estas propiedades.

El sistema de coordenadas 3D implicado por Xamarin.Forms es zurdo. Si apunta el dedo índice de la mano izquierda en la dirección de aumentar las coordenadas X (a la derecha) y su dedo medio en la dirección de aumentar las coordenadas Y (hacia abajo), su pulgar apunta en la dirección de aumentar las coordenadas Z (fuera de la pantalla).

Además, para cualquiera de los tres ejes, si apunta con el pulgar izquierdo en la dirección de los valores crecientes, la curva de los dedos indica la dirección de rotación para ángulos de rotación positivos.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 21 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Ejemplos del capítulo 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
