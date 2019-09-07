---
title: Resumen del capítulo 22. Animación
description: 'Crear Mobile Apps con Xamarin. Forms: Resumen del capítulo 22. Animación'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770994"
---
# <a name="summary-of-chapter-22-animation"></a>Resumen del capítulo 22. Animación

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Hemos visto que puede crear sus propias animaciones utilizando el temporizador de Xamarin.Forms o `Task.Delay`, pero generalmente es más fácil usar las prestaciones de animación proporcionadas por Xamarin.Forms. Tres clases implementan estas animaciones:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), el enfoque de alto nivel
- [`Animation`](xref:Xamarin.Forms.Animation), más versátil, pero más difícil
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), un enfoque más versátil, nivel más bajo

Por lo general, las animaciones como destino las propiedades que están respaldadas por propiedades enlazables. Esto no es un requisito, pero estas son las únicas propiedades dinámicamente reaccione ante los cambios.

No hay ninguna interfaz XAML para estas animaciones, pero puede integrar las animaciones en XAML mediante las técnicas descritas en [ **capítulo 23. Los desencadenadores y comportamientos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Exploración de las animaciones básicas

Las funciones de animación básica son métodos de extensión encontrados en el [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase. Estos métodos se aplican a cualquier objeto que se deriva de `VisualElement`. Las animaciones más sencillas tener como destino las transformaciones que se describen las propiedades en [ `Chapter 21. Transforms` ](chapter21.md).

El [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) muestra cómo el `Clicked` controlador de eventos para un `Button` puede llamar a la [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensión para poner en la botón en un círculo.

El `RotateTo` del método cambia la `Rotation` propiedad de la `Button` de 0 a 360 durante el transcurso de un cuarto segundo (de forma predeterminada). Si el `Button` se pulsa una vez más, sin embargo, no hace nada porque el `Rotation` propiedad ya es 360 grados.

### <a name="setting-the-animation-duration"></a>Establecer la duración de la animación

El segundo argumento `RotateTo` corresponde a una duración en milisegundos. Si establece en un valor grande, pulsando el `Button` durante una animación se inicia una nueva animación, empezando por el ángulo actual.

### <a name="relative-animations"></a>Animaciones relativas

El `RelRotateTo` método realiza un giro relativo mediante la adición de un valor especificado al valor existente. Este método permite el `Button` puntear varias veces y cada vez aumenta el `Rotation` propiedad 360 grados.

### <a name="awaiting-animations"></a>En espera de las animaciones

Todos los métodos de animación de `ViewExtensions` devolver `Task<bool>` objetos. Esto significa que puede definir una serie de animaciones secuenciales con `ContinueWith` o `await`. El `bool` finalización de valor devuelto es `false` si la animación ha finalizado sin interrupción o `true` si se ha cancelado la [ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) método, lo que cancela todas las animaciones iniciadas por el otro método `ViewExtensions` que se establecen en el mismo elemento.

### <a name="composite-animations"></a>Animaciones de composición

Puede mezclar las animaciones esperadas y que no es "awaited" para crear animaciones compuestas. Estas son las animaciones en `ViewExtensions` que tienen como destino el `TranslationX`, `TranslationY`, y `Scale` las propiedades de transformación:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Tenga en cuenta que `TranslateTo` afecta potencialmente al tanto del `TranslationX` y `TranslationY` propiedades.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll y Task.WhenAny

También es posible administrar animaciones simultáneas mediante [ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*), que señala al todos varias tareas se han concluido, y [ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*), que indica cuándo el primero de varios ha finalizado las tareas.

### <a name="rotation-and-anchors"></a>Rotación y delimitadores

Cuando se llama a la `ScaleTo`, `RelScaleTo`, `RotateTo`, y `RelRotateTo` métodos, puede establecer el [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) y [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propiedades para indicar el Centro de escala y rotación.

El [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demuestra esta técnica mediante la revolución un `Button` en torno al centro de la página.

### <a name="easing-functions"></a>Funciones de aceleración

Por lo general, las animaciones son lineales de un valor inicial para un valor final. Funciones de aceleración pueden provocar animaciones acelerar o ralentizar en su transcurso. El último argumento opcional a los métodos de la animación es de tipo [ `Easing` ](xref:Xamarin.Forms.Easing), una clase que defina 11 campos estáticos de sólo lectura de tipo `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), el valor predeterminado
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut), y [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut), y [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) y [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) y [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

El `In` sufijo indica que el efecto está al principio de la animación, `Out` significa que al final, y `InOut` significa que se encuentra al principio y al final de la animación.

El [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) ejemplo muestra el uso de funciones de aceleración.

### <a name="your-own-easing-functions"></a>Sus propias funciones de aceleración

También se puede definir funciones de aceleración propias pasando un [ `Func<double, double>` ](xref:System.Func`2) a la [ `Easing` constructor](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` También define una conversión implícita de `Func<double, double>` a `Easing`. El argumento para la función de aceleración es siempre en el intervalo de 0 a 1 como la animación continúa linealmente de principio a fin. La función *normalmente* devuelve un valor en el intervalo de 0 a 1, pero podría ser brevemente negativo o mayor que 1 (como sucede con la `SpringIn` y `SpringOut` funciones) o podría romper las reglas si sabe lo que está haciendo.

El [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) muestra una función de aceleración personalizada y [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) muestra otra.

El [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) ejemplo también muestra una función de aceleración personalizada y también una técnica de cambiar el `AnchorX` y `AnchorY` propiedades dentro de una secuencia de animaciones de rotación.

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tiene un [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) función de clase que usa una aceleración personalizada para mover un botón cuando se hace clic en. El [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) ejemplo muestra.

### <a name="entrance-animations"></a>Animaciones de entrada

Un tipo más popular de animación se produce cuando una página aparece por primera vez. Se puede iniciar una animación de este tipo el [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) la invalidación de la página. Para estas animaciones, se recomienda configurar el XAML de cómo desea que la página aparezca *después* la animación y, a continuación, inicializar y animar el diseño desde el código.

El [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) ejemplo se utiliza el [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensión para difuminar el contenido de la página.

El [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) ejemplo se utiliza el [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensión para el contenido de la página se deslizan desde los lados.

El [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) ejemplo se utiliza el [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensión se va a animar el `RotationY` propiedad. Un [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método también está disponible.

### <a name="forever-animations"></a>Animaciones para siempre

En el otro extremo, "forever" las animaciones se ejecutan hasta que finaliza el programa. Por lo general, estos están diseñados para fines de demostración.

El [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) ejemplo usa [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animación de fundido de entrada y salida dos fragmentos de texto.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) muestra palíndromo y secuencialmente gira las letras individuales 180 grados para que sean todas al revés. A continuación, la cadena completa se voltea 180 grados para leer el mismo que la cadena original.

El [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) ejemplo gira una sencilla `BoxView` helicóptero al pueda girar en torno al centro de la pantalla.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` radios en torno al centro de la pantalla y, a continuación, gira cada radio para crear patrones interesantes:

[![Captura de pantalla triple de rotación de radios](images/ch22fg21-small.png "girar radios")](images/ch22fg21-large.png#lightbox "girar radios")

Sin embargo, progresivamente aumentando la `Rotation` propiedad de un elemento podría no funcionar a largo plazo, como el [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) muestra.

El [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) ejemplo usa [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), y [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para hacer parecer como si gira un mapa de bits en un espacio 3D.

### <a name="animating-the-bounds-property"></a>Animar la propiedad bounds

El único método de extensión en `ViewExtensions` aún no se muestra es [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que anima eficazmente solo lectura [ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds) propiedad mediante una llamada a la [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método. Normalmente, este método se llama `Layout` derivados, tal como se explicará en [ **capítulo 26. CustomLayouts**](chapter26.md).

El `LayoutTo` método deben limitarse a fines especiales. El [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) programa lo usa para comprimir y expandir un `BoxView` como rebota de los lados de una página.

El [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) ejemplo usa `LayoutTo` mover los mosaicos de una implementación de clásica rompecabezas de 15-16 que se muestra una imagen codificada en lugar de iconos numerados:

[![Captura de pantalla triple de Xamarin Xuzzle](images/ch22fg26-small.png "juego del rompecabezas de Xuzzle")](images/ch22fg26-large.png#lightbox "Xuzzle juegos de rompecabezas")

### <a name="your-own-awaitable-animations"></a>Sus propias animaciones esperables

El [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) ejemplo crea una animación que admite await. La clase fundamental que puede devolver un `Task` objeto desde el método y una señal cuando se completa la animación es [ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Más en las animaciones

El sistema de animación de Xamarin.Forms puede ser un poco confuso. Además el `Easing` (clase), el sistema de animación comprende el `ViewExtensions`, `Animation`, y `AnimationExtension` clases.

### <a name="viewextensions-class"></a>Clase ViewExtensions

Ya hemos visto [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions). Define nueve métodos que devuelven `Task<bool>` y [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Siete de destino nueve métodos propiedades de transformación. Los otros dos son [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), los destinos que el [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propiedad, y [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que llama a la [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método.

### <a name="animation-class"></a>Clase de animación

El [ `Animation` ](xref:Xamarin.Forms.AnimationExtensions) clase tiene un [constructor](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) con cinco argumentos para definir métodos terminados y devolución de llamada y los parámetros de la animación.

Se pueden agregar animaciones secundarias con [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))y la sobrecarga de [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

El objeto de animación, a continuación, se inicia con una llamada a la [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método.

### <a name="animationextensions-class"></a>Clase AnimationExtensions

El [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) clase contiene principalmente los métodos de extensión. Hay varias versiones de un `Animate` método y el tipo genérico [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método es tan versátil que realmente es la función de animación única que necesita.

## <a name="working-with-the-animation-class"></a>Trabajar con la clase de animación

El [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) muestra el [ `Animation` ](xref:Xamarin.Forms.Animation) clase con varias animaciones diferentes.

### <a name="child-animations"></a>Animaciones secundarias

El [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) ejemplo también muestra secundarios animaciones, que hacen que el uso de la (muy similar) [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) y [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) métodos.

### <a name="beyond-the-high-level-animation-methods"></a>Más allá de los métodos de alto nivel de animación

El [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) ejemplo también muestra cómo realizar animaciones en las que van más allá de las propiedades de destino por la `ViewExtensions` métodos. En un ejemplo, una serie de períodos más largas; en otro ejemplo, un `BackgroundColor` propiedad está animada.

### <a name="more-of-your-own-awaitable-methods"></a>Varias de sus propios métodos que admite await

El [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método `ViewExtensions` no funciona con el [ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) función. Se detiene cuando la salida de entradas y salidas lenta que se obtiene por encima de 1.

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) clase con [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) y [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) métodos de extensión que no tienen este problema, así como [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) y [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) métodos para cancelarlos animaciones.

El [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) muestra el `TranslateXTo` método.

El `MoreExtensions` clase también contiene un [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) método de extensión que combina la traducción de X e Y, y un [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) método.

### <a name="implementing-a-bezier-animation"></a>Implementación de una animación de Bézier

También es posible desarrollar una animación que mueve un elemento a lo largo de la ruta de acceso de una curva spline de Bézier. El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) estructura que encapsula una curva spline de Bézier y un [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumeración a la orientación del control.

El [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) clase contiene un [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) método de extensión y un [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) método.

El [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) muestra la animación de un elemento a lo largo de una ruta de acceso Beizer.

## <a name="working-with-animationextensions"></a>Trabajar con AnimationExtensions

Un tipo de animación que faltan en la colección estándar es una animación de color. El problema es que no hay ninguna manera correcta para interpolar entre dos `Color` valores. Es posible interpolar los valores RGB individuales, pero solo como válidos es interpolar HSL (valores).

Por este motivo, el [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene dos `Color` métodos de animación: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)y [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (También hay dos métodos de cancelación: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) y [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Ambos métodos convierten el uso de [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), que realiza la animación mediante una llamada a una amplia genérico [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions).

El [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) ejemplo muestra cómo utilizar estos dos tipos de animaciones de color.

## <a name="structuring-your-animations"></a>Estructurar las animaciones

A veces resulta útil expresar las animaciones en XAML y usarlos junto con MVVM. Este tema se trata en el próximo capítulo, [ **capítulo 23. Los desencadenadores y comportamientos**](chapter23.md).

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 22 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Ejemplos de capítulo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animación](~/xamarin-forms/user-interface/animation/index.md)
