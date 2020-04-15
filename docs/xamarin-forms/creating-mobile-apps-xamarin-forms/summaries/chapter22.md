---
title: Resumen del capítulo 22. Animación
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 22. Animación'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770994"
---
# <a name="summary-of-chapter-22-animation"></a>Resumen del capítulo 22. Animación

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Hemos observado que puede crear sus propias animaciones con el temporizador de Xamarin.Forms o `Task.Delay`, pero generalmente es más fácil usar las prestaciones de animación proporcionadas por Xamarin.Forms. Hay tres clases que implementan estas animaciones:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), el enfoque de alto nivel
- [`Animation`](xref:Xamarin.Forms.Animation), más versátil, pero más difícil
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), el enfoque más versátil y de nivel más bajo

Por lo general, las animaciones tienen como destino propiedades respaldadas por propiedades enlazables. No se trata de un requisito, pero son las únicas propiedades que reaccionan de forma dinámica a los cambios.

No hay ninguna interfaz XAML para estas animaciones, pero puede integrar animaciones en XAML mediante las técnicas descritas en [**Capítulo 23. Desencadenadores y comportamientos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Exploración de las animaciones básicas

Las funciones de animación básicas son métodos de extensión que se encuentran en la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions). Estos métodos se aplican a cualquier objeto derivado de `VisualElement`. Las animaciones más sencillas apuntan a las propiedades de transformaciones que se describen en [`Chapter 21. Transforms`](chapter21.md).

[**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) muestra cómo el controlador de eventos `Clicked` de un elemento `Button` puede llamar al método de extensión [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para girar el botón en un círculo.

El método `RotateTo` cambia la propiedad `Rotation` de `Button` de 0 a 360 en un cuarto de segundo (de forma predeterminada). Sin embargo, si `Button` se vuelve a pulsar, no hace nada porque la propiedad `Rotation` ya es 360 grados.

### <a name="setting-the-animation-duration"></a>Establecer la duración de la animación

El segundo argumento para `RotateTo` es una duración en milisegundos. Si se establece en un valor grande, al pulsar en `Button` durante una animación, se inicia una nueva animación que comienza en el ángulo actual.

### <a name="relative-animations"></a>Animaciones relativas

El método `RelRotateTo` realiza una rotación relativa agregando un valor especificado al valor existente. Este método permite que `Button` se pulse varias veces y, cada vez, aumenta la propiedad `Rotation` en 360 grados.

### <a name="awaiting-animations"></a>Animaciones en espera

Todos los métodos de animación de `ViewExtensions` devuelven objetos `Task<bool>`. Esto significa que puede definir una serie de animaciones secuenciales mediante `ContinueWith` o `await`. El valor devuelto de finalización `bool` es `false` si la animación ha finalizado sin interrupción o `true` si la ha cancelado el método [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)), que cancela todas las animaciones iniciadas por el otro método en `ViewExtensions` que se establecen en el mismo elemento.

### <a name="composite-animations"></a>Animaciones compuestas

Puede mezclar animaciones esperadas y no esperadas para crear animaciones compuestas. Estas son las animaciones de `ViewExtensions` que tienen como destino las propiedades de transformación `TranslationX`, `TranslationY` y `Scale`:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Tenga en cuenta que `TranslateTo` puede afectar a las propiedades `TranslationX` y `TranslationY`.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll y Task.WhenAny

También es posible administrar animaciones simultáneas mediante [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*), que indica cuándo se han concluido varias tareas, y [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*), que indica cuándo ha concluido la primera de varias tareas.

### <a name="rotation-and-anchors"></a>Rotación y delimitadores

Cuando se llama a los métodos `ScaleTo`, `RelScaleTo`, `RotateTo` y `RelRotateTo`, se pueden establecer las propiedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) y [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) para indicar el centro del escalado y la rotación.

[**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demuestra esta técnica mediante el giro de un elemento `Button` en torno al centro de la página.

### <a name="easing-functions"></a>Funciones de aceleración

Por lo general, las animaciones son lineales desde un valor inicial hasta un valor final. Las funciones de aceleración pueden hacer que las animaciones aceleren o ralenticen su curso. El último argumento opcional de los métodos de animación es de tipo [`Easing`](xref:Xamarin.Forms.Easing), una clase que define 11 campos estáticos de solo lectura de tipo `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), la opción predeterminada
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) y [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) y [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) y [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) y [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

El sufijo `In` indica que el efecto está al principio de la animación, `Out` significa al final y `InOut` significa que está al principio y al final de la animación.

En el ejemplo [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) se muestra el uso de las funciones de aceleración.

### <a name="your-own-easing-functions"></a>Sus propias funciones de aceleración

También puede definir sus propias funciones de aceleración pasando un elemento [`Func<double, double>`](xref:System.Func`2) al [constructor `Easing`](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` también define una conversión implícita de `Func<double, double>` a `Easing`. El argumento para la función de aceleración siempre está en el rango de 0 a 1, ya que la animación continúa linealmente desde el principio hasta el final. La función *normalmente* devuelve un valor en el rango de 0 a 1, pero, durante poco tiempo, podría ser negativo o mayor que 1 (como sucede con las funciones `SpringIn` y `SpringOut`) o podría romper las reglas si sabe lo que está haciendo.

En el ejemplo [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) se muestra una función de aceleración personalizada, y [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) muestra otra.

En el ejemplo [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) también se muestra una función de aceleración personalizada, así como una técnica para cambiar las propiedades `AnchorX` y `AnchorY` en una secuencia de animaciones de rotación.

La biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tiene una clase [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) que usa una función de aceleración personalizada para mover un botón cuando se hace clic en él. En el ejemplo [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) se muestra este comportamiento.

### <a name="entrance-animations"></a>Animaciones de entrada

Se produce un tipo popular de animación cuando aparece por primera vez una página. Esta animación se puede iniciar en la anulación del método [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) de la página. Para estas animaciones, lo mejor es configurar el código XAML para el modo en que desea que aparezca la página *después de* la animación y, a continuación, inicializar y animar el diseño desde el código.

En el ejemplo [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) se usa el método de extensión [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para atenuar el contenido de la página.

En el ejemplo [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) se usa el método de extensión [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) para deslizar el contenido de la página desde los lados.

En el ejemplo [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) se usa el método de extensión [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar la propiedad `RotationY`. También hay disponible un método [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)).

### <a name="forever-animations"></a>Animaciones indefinidas

En el otro extremo, las animaciones "indefinidas" se ejecutan hasta que finaliza el programa. Por lo general, están pensadas para fines de demostración.

En el ejemplo [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) se usa la animación [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para atenuar dos partes de texto dentro y fuera.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) muestra un palíndromo y, a continuación, gira secuencialmente las letras individuales en 180 grados, de modo que estén todas invertidas. A continuación, toda la cadena se voltea 180 grados para leer lo mismo que en la cadena original.

En el ejemplo [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation), rota un sencillo helicóptero `BoxView` mientras este gira en torno al centro de la pantalla.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` radios en torno al centro de la pantalla y, a continuación, gira cada radio para crear patrones interesantes:

[![Captura de pantalla triple de radios giratorios](images/ch22fg21-small.png "Radios rotativos")](images/ch22fg21-large.png#lightbox "Radios rotativos")

Sin embargo, el aumento progresivo de la propiedad `Rotation` de un elemento podría no funcionar a largo plazo, como se muestra en el ejemplo [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown).

En el ejemplo [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) se usa [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))[, `RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))[y `RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para que parezca como si un mapa de bits está girando en el espacio 3D.

### <a name="animating-the-bounds-property"></a>Animar la propiedad enlazada

El único método de extensión de `ViewExtensions` que todavía no se ha mostrado es [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que anima eficazmente la propiedad [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) de solo lectura llamando al método [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)). Los derivados `Layout` normalmente llaman a este método, como se tratará en el [**Capítulo 26. CustomLayouts**](chapter26.md).

El método `LayoutTo` se debe restringir a propósitos especiales. El programa [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) lo usa para comprimir y expandir un elemento `BoxView` mientras rebota fuera de los lados de una página.

El ejemplo [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) usa `LayoutTo` para mover iconos en una implementación del rompecabezas clásico de 15-16 que muestra una imagen codificada en lugar de mosaicos numerados:

[![Captura de pantalla triple de Xamarin Xuzzle](images/ch22fg26-small.png "Juego de puzles Xuzzle")](images/ch22fg26-large.png#lightbox "Juego de puzles Xuzzle")

### <a name="your-own-awaitable-animations"></a>Animaciones propias que admiten await

El ejemplo [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) crea una animación que admite await. La clase fundamental que puede devolver un objeto `Task` desde el método y señalar cuando se completa la animación es [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Animaciones más profundas

El sistema de animación de Xamarin.Forms puede ser un poco confuso. Además de la clase `Easing`, el sistema de animación consta de las clases `ViewExtensions`, `Animation` y `AnimationExtension`.

### <a name="viewextensions-class"></a>Clase ViewExtensions

Ya ha visto [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions). Define nueve métodos que devuelven `Task<bool>` y [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Siete de los nueve métodos que tienen como destino las propiedades de transformación. Los otros dos son [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), que tiene como destino la propiedad [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity), y [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que llama al método [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)).

### <a name="animation-class"></a>Clase Animation

La clase [`Animation`](xref:Xamarin.Forms.AnimationExtensions) tiene un [constructor](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) con cinco argumentos para definir los métodos de devolución de llamada y finalización, y los parámetros de la animación.

Se pueden agregar animaciones secundarias con [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) y la sobrecarga de [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

A continuación, el objeto de animación se inicia con una llamada al método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})).

### <a name="animationextensions-class"></a>Clase AnimationExtensions

La clase [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) contiene principalmente métodos de extensión. Hay varias versiones de un método `Animate`, y el método genérico [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) es tan versátil que es realmente la única función de animación que necesita.

## <a name="working-with-the-animation-class"></a>Trabajar con la clase Animation

En el ejemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) se muestra la clase [`Animation`](xref:Xamarin.Forms.Animation) con varias animaciones diferentes.

### <a name="child-animations"></a>Animaciones secundarias

En el ejemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) también se muestran animaciones secundarias, que hacen uso de los métodos [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) y [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) (muy similares).

### <a name="beyond-the-high-level-animation-methods"></a>Más allá de los métodos de animación de alto nivel

En el ejemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) también se muestra cómo realizar animaciones que van más allá de las propiedades a las que se destinan los métodos `ViewExtensions`. En un ejemplo, una serie de períodos es más larga; en otro ejemplo, una propiedad `BackgroundColor` es animada.

### <a name="more-of-your-own-awaitable-methods"></a>Más de sus propios métodos que admiten await

El método [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) de `ViewExtensions` no funciona con la función [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut). Se detiene cuando la salida de aceleración supera 1.

La biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una clase [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) con los métodos de extensión [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) y [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) que no tienen este problema, así como los métodos [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) y [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) para cancelar esas animaciones.

[**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) muestra el método `TranslateXTo`.

La clase `MoreExtensions` también contiene un método de extensión [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) que combina la traducción X e Y, y un método [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113).

### <a name="implementing-a-bezier-animation"></a>Implementar una animación Bézier

También es posible desarrollar una animación que mueva un elemento a lo largo del trazado de una curva B-spline. La biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una estructura [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) que encapsula una curva B-spline y una enumeración [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) para controlar la orientación.

La clase [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) contiene un método de extensión [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) y un método [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161).

En el ejemplo [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) se muestra cómo animar un elemento a lo largo de un trazado Béizer.

## <a name="working-with-animationextensions"></a>Trabajar con AnimationExtensions

Un tipo de animación que falta en la colección estándar es una animación de color. El problema es que no hay ninguna manera adecuada de interpolar entre dos valores `Color`. Es posible interpolar los valores RGB individuales, pero igual de válido es interpolar valores HSL.

Por esta razón, la clase [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) de la biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene dos métodos de animación `Color`: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) y [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (También hay dos métodos de cancelación: [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) y [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Ambos métodos hacen uso de [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), que realiza la animación mediante una llamada al método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) genérico en [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions).

En el ejemplo [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) se muestra el uso de estos dos tipos de animaciones de color.

## <a name="structuring-your-animations"></a>Estructurar las animaciones

A veces resulta útil expresar animaciones en XAML y usarlas junto con MVVM. Esto se trata en el capítulo siguiente, [**Capítulo 23. Desencadenadores y comportamientos**](chapter23.md).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 22 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Ejemplos del capítulo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animación](~/xamarin-forms/user-interface/animation/index.md)
