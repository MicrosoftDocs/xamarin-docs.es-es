---
title: Animaciones sencillas en Xamarin.Forms
description: La clase ViewExtensions proporciona métodos de extensión que se pueden usar para construir las animaciones sencillas. En este artículo muestra cómo crear y cancelar las animaciones mediante la clase ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: d3046479282ef1535677f6712e487ab80bbf9d1b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656802"
---
# <a name="simple-animations-in-xamarinforms"></a>Animaciones sencillas en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La clase ViewExtensions proporciona métodos de extensión que se pueden usar para construir las animaciones sencillas. En este artículo muestra cómo crear y cancelar las animaciones mediante la clase ViewExtensions._


El [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase proporciona los siguientes métodos de extensión que pueden usarse para crear animaciones sencillas:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima el [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) las propiedades de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.VisualElement.Scale) anima el [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) se aplica un aumento incremental animado o disminución en el [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima el [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) se aplica un aumento incremental animado o disminución en el [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima el [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima el [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima el [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propiedad de un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

De forma predeterminada, cada animación tardará 250 milisegundos. Sin embargo, se puede especificar una duración de cada animación al crear la animación.

El [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase también incluye un [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) método que puede usarse para cancelar las animaciones.

> [!NOTE]
> El [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase proporciona un [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) método de extensión. Sin embargo, este método está pensado para usarse diseños para animar las transiciones entre Estados de diseño que contienen el tamaño y posición de los cambios. Por lo tanto, que debe usarse únicamente por [ `Layout` ](xref:Xamarin.Forms.Layout) subclases.

Los métodos de extensión de la animación de la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) son asincrónicos y devuelven un objeto `Task<bool>`. El valor devuelto es `false` si se completa la animación y `true` si esta se cancela. Por lo tanto, los métodos de animación normalmente deben usarse con el operador `await`, lo que permite determinar fácilmente cuándo se ha completado una animación. Además, después se pueden crear animaciones secuenciales con métodos de animación posteriores que se ejecutan después de que se haya completado el método anterior. Para obtener más información, consulte [Animaciones compuestas](#compound).

Si hay un requisito para permitir que una animación completa en segundo plano, el `await` se puede omitir el operador. En este escenario, los métodos de extensión de la animación se devolverá rápidamente después de iniciar la animación, con la animación que se producen en segundo plano. Esta operación puede aprovecharse al crear animaciones compuestas. Para obtener más información, consulte [animaciones compuesto](#composite).

Para obtener más información sobre la `await` operador, consulte [información general de soporte técnico de Async](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animaciones únicas

Cada método de extensión en el [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) implementa una operación de animación única que progresivamente cambia una propiedad de un valor a otro valor durante un período de tiempo. Esta sección explora cada operación de animación.

### <a name="rotation"></a>Giro

En el ejemplo de código siguiente se muestra cómo utilizar el [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método se va a animar el [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Este código se anima la [ `Image` ](xref:Xamarin.Forms.Image) instancia girando hasta 360 grados superior a 2 segundos (2.000 milisegundos). El [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtiene la actual [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad valor para el inicio de la animación y, a continuación, se gira desde ese valor a su primer argumento (360). Una vez que la animación se complete, la imagen [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad se restablece a 0. Esto garantiza que el `Rotation` propiedad no se queda en 360 después de que concluya la animación, que podrían impedir que las rotaciones adicionales.

Las capturas de pantalla siguientes muestran la rotación en curso en cada plataforma:

![](simple-images/rotateto.png "Animación de giro")

### <a name="relative-rotation"></a>Giro relativa

En el ejemplo de código siguiente se muestra cómo utilizar el [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método para aumentar o disminuir de forma incremental el [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Este código se anima la [ `Image` ](xref:Xamarin.Forms.Image) instancia girando superior a 2 segundos (2.000 milisegundos) de 360 grados desde su posición inicial. El [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtiene la actual [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad valor para el inicio de la animación y, a continuación, se gira desde ese valor para el valor más su primer argumento (360). Esto garantiza que cada animación siempre será un giro de 360 grados desde la posición inicial. Por lo tanto, si se invoca una nueva animación mientras una animación ya está en curso, se iniciará desde la posición actual y puede terminar en una posición que no es un incremento de 360 grados.

Las capturas de pantalla siguientes muestran el giro relativo en curso en cada plataforma:

![](simple-images/relrotateto.png "Animación de giro relativa")

### <a name="scaling"></a>Cambiar escala

En el ejemplo de código siguiente se muestra cómo utilizar el [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) método se va a animar el [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Este código se anima la [ `Image` ](xref:Xamarin.Forms.Image) instancia mediante el escalamiento vertical a dos veces su tamaño superior a 2 segundos (2.000 milisegundos). El [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) método obtiene la actual [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) el valor de propiedad (valor predeterminado de 1) para el inicio de la animación y, a continuación, se escala desde ese valor a su primer argumento (2). Esto tiene el efecto de aumentar el tamaño de la imagen a dos veces su tamaño.

Las capturas de pantalla siguientes muestran el escalado en curso en cada plataforma:

![](simple-images/scaleto.png "Ajuste de escala de animación")

> [!NOTE]
> El [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) también define la clase [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) y [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) propiedades, que se pueden escalar el `VisualElement` manera diferente en el direcciones horizontal y vertical. Estas propiedades se pueden animar con la [ `Animation` ](xref:Xamarin.Forms.Animation) clase. Para obtener más información, consulte [animaciones personalizadas en Xamarin.Forms](custom.md).

### <a name="relative-scaling"></a>Relativa al ajuste de escala

En el ejemplo de código siguiente se muestra cómo utilizar el [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método se va a animar el [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Este código se anima la [ `Image` ](xref:Xamarin.Forms.Image) instancia mediante el escalamiento vertical a dos veces su tamaño superior a 2 segundos (2.000 milisegundos). El [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtiene la actual [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) valor de propiedad para el inicio de la animación y, a continuación, se escala desde ese valor para el valor más su primer argumento (2). Esto garantiza que cada animación siempre será un escalamiento de 2 desde la posición inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Escalado y la rotación con delimitadores

El [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) y [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propiedades establecen el centro de ajuste de escala o rotación para el [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) y [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedades. Por lo tanto, sus valores también afectan a la [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) y [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) métodos.

Dado un [ `Image` ](xref:Xamarin.Forms.Image) que se ha colocado en el centro de un diseño, en el ejemplo de código siguiente se muestra la rotación de la imagen en torno al centro del diseño estableciendo su [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propiedad:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Para girar el [ `Image` ](xref:Xamarin.Forms.Image) instancia en torno al centro del diseño, el [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) y [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propiedades deben establecerse en valores en relación con el ancho y alto de la `Image`. En este ejemplo, el centro de la `Image` se define como en el centro del diseño de modo que el valor predeterminado `AnchorX` valor de 0,5 no es necesario cambiar. Sin embargo, el `AnchorY` propiedad se vuelve a definir para que sea un valor de la parte superior de la `Image` al punto central del diseño. Esto garantiza que el `Image` hace una rotación completa de 360 grados alrededor del punto central del diseño, como se muestra en las capturas de pantalla siguiente:

![](simple-images/rotate-anchors.png "Animación de giro con delimitadores")

### <a name="translation"></a>Conversión

En el ejemplo de código siguiente se muestra cómo utilizar el [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método se va a animar el [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) y [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) las propiedades de un [ `Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Este código se anima la [ `Image` ](xref:Xamarin.Forms.Image) instancia convirtiendo, vertical y horizontalmente en 1 segundo (1000 milisegundos). El [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método traduce al mismo tiempo la imagen 100 píxeles a la izquierda y 100 píxeles hacia arriba. Esto es porque el primer y segundo argumentos son ambos números negativos. Proporcionar los números positivos traduciría la imagen a la derecha y abajo.

Las capturas de pantalla siguientes muestran la traducción en curso en cada plataforma:

![](simple-images/translateto.png "Animación de traducción")

> [!NOTE]
> Si un elemento se distribuyen inicialmente fuera de la pantalla y, a continuación, se traduce en la pantalla, después de la traducción, diseño de entrada del elemento permanece fuera de la pantalla y el usuario no puede interactuar con él. Por lo tanto, se recomienda que una vista debe disponerse en su posición final y, a continuación, los necesarios traducciones realizadas.

### <a name="fading"></a>Corrección selectiva

En el ejemplo de código siguiente se muestra cómo utilizar el [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método se va a animar el [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Este código se anima la [ `Image` ](xref:Xamarin.Forms.Image) instancia por desvanezcan en más de 4 segundos (de 4000 milisegundos). El [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtiene la actual [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) valor de propiedad para el inicio de la animación y, a continuación, fundidos en desde ese valor a su primer argumento (1).

Las capturas de pantalla siguientes muestran la atenuación en curso en cada plataforma:

![](simple-images/fadeto.png "Animación de atenuación")

<a name="compound" />

## <a name="compound-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación secuencial de las animaciones y se pueden crear con el `await` operador, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

En este ejemplo, el [ `Image` ](xref:Xamarin.Forms.Image) se traduce en 6 segundos (6.000 milisegundos). La traducción de la `Image` utiliza cinco animaciones con la `await` operador que indica que cada animación se ejecuta de forma secuencial. Por lo tanto, los métodos de la animación siguiente ejecutan después de que se ha completado el método anterior.

<a name="composite" />

## <a name="composite-animations"></a>Animaciones de composición

Una animación compuesta es una combinación de animaciones donde se ejecutan simultáneamente dos o más de las animaciones. Las animaciones de composición se pueden crear mezclando animaciones esperadas y que no es "awaited", como se muestra en el ejemplo de código siguiente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

En este ejemplo, el [ `Image` ](xref:Xamarin.Forms.Image) es escalar y girar simultáneamente más de 4 segundos (de 4000 milisegundos). El escalado de la `Image` utiliza dos animaciones secuenciales que se producen al mismo tiempo como la rotación. El [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método se ejecuta sin un `await` operador y se devuelve inmediatamente, con la primera [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) , a continuación, a partir de animación. El `await` operador en la primera `ScaleTo` retrasa la llamada al método el segundo `ScaleTo` hasta la primera llamada a un método `ScaleTo` ha completado la llamada al método. En este momento la `RotateTo` animación es la mitad forma completa y el `Image` estará gira 180 grados. Durante los últimos 2 segundos (2.000 milisegundos), el segundo `ScaleTo` animación y `RotateTo` animación ambos completar.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Ejecución simultánea de varios métodos asincrónicos

El `static` `Task.WhenAny` y `Task.WhenAll` métodos se utilizan para ejecutar varios métodos asincrónicos simultáneamente y, por lo tanto, puede usarse para crear animaciones compuestas. Ambos métodos devuelven un `Task` de objetos y Aceptar una colección de métodos que cada valor devuelto una `Task` objeto. El `Task.WhenAny` método se completa cuando cualquier método en su colección completa su ejecución, como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

En este ejemplo, el `Task.WhenAny` llamada de método contiene dos tareas. La primera tarea gira la imagen más de 4 segundos (de 4000 milisegundos) y la segunda tarea escala la imagen superior a 2 segundos (2.000 milisegundos). Cuando se completa la segunda tarea, el `Task.WhenAny` se completa la llamada al método. Sin embargo, aunque el [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método todavía se está ejecutando, el segundo [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) método puede comenzar.

El `Task.WhenAll` método se completa cuando se hayan completado todos los métodos en su colección, como se muestra en el ejemplo de código siguiente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

En este ejemplo, el `Task.WhenAll` llamada de método contiene tres tareas, cada uno de los cuales se ejecuta más de 10 minutos. Cada `Task` hace que sea un número diferente de rotaciones de 360 grados: 307 rotaciones para [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotaciones para [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))y 199 rotaciones para [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Estos valores son números primos, por lo tanto, lo que garantiza que las rotaciones no están sincronizadas y, por tanto, no origine patrones repetitivos.

Las capturas de pantalla siguientes muestran las rotaciones varias en curso en cada plataforma:

![](simple-images/multiple-rotations.png "Animación compuesto")

## <a name="canceling-animations"></a>Cancelar las animaciones

Una aplicación puede cancelar una o varias animaciones con una llamada a la `static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) método, como se muestra en el ejemplo de código siguiente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Esta operación cancelará inmediatamente todas las animaciones que se están ejecutando en el [ `Image` ](xref:Xamarin.Forms.Image) instancia.

## <a name="summary"></a>Resumen

En este artículo se muestra creando y cancelación de animaciones con la [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase. Esta clase proporciona métodos de extensión que se pueden usar para construir las animaciones sencillas que giran, escalarán, traducirán y atenuación [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instancias.


## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Animación básica (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
