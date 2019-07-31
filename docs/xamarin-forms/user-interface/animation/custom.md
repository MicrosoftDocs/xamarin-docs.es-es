---
title: Animaciones personalizadas en Xamarin.Forms
description: En este artículo se muestra cómo usar la clase de animación de Xamarin.FOrms para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan las propiedades que no se animación mediante los métodos de animación existentes.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: b195e63bcc88c4d1c659216f99ab698773f73e9e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656810"
---
# <a name="custom-animations-in-xamarinforms"></a>Animaciones personalizadas en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La clase de animación es el bloque de creación de todas las animaciones de Xamarin.Forms, con los métodos de extensión en la clase ViewExtensions crear uno o varios objetos de animación. En este artículo se muestra cómo usar la clase de animación para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan las propiedades que no se animación mediante los métodos de animación existentes._


Se debe especificar un número de parámetros al crear un `Animation` objeto, incluidos los valores inicial y final de la propiedad que se anima y una devolución de llamada que cambia el valor de la propiedad. Un `Animation` objeto también puede mantener una colección de animaciones secundarias que se pueden ejecutar y sincronizado. Para obtener más información, consulte [animaciones secundarias](#child).

Ejecuta una animación que se creó con la [ `Animation` ](xref:Xamarin.Forms.Animation) (clase), que puede o no incluir animaciones secundarias, se logra mediante una llamada a la [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método. Este método especifica la duración de la animación y, entre otros elementos, una devolución de llamada que controla si se debe repetir la animación.

## <a name="creating-an-animation"></a>Creación de una animación

Al crear un [ `Animation` ](xref:Xamarin.Forms.Animation) objeto, por lo general, un mínimo de tres parámetros son necesarios, como se muestra en el ejemplo de código siguiente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Este código define una animación de la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image) la instancia de un valor de 1 a un valor de 2. El valor animado, que se deriva mediante Xamarin.Forms, se pasa a la devolución de llamada especificado como el primer argumento, donde se usa para cambiar el valor de la `Scale` propiedad.

La animación se inicia con una llamada a la [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método, como se muestra en el ejemplo de código siguiente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Tenga en cuenta que el [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método no devuelve un `Task` objeto. En su lugar, las notificaciones se proporcionan a través de métodos de devolución de llamada.

Los siguientes argumentos se especifican en el `Commit` método:

- El primer argumento (*propietario*) identifica al propietario de la animación. Esto puede ser el elemento visual en el que se aplica la animación u otro elemento visual, como la página.
- El segundo argumento (*nombre*) identifica la animación con un nombre. El nombre se combina con el propietario para identificar de forma única la animación. Esta identificación única, a continuación, puede usarse para determinar si se está ejecutando la animación ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))), o para cancelarla ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- El tercer argumento (*tasa*) indica el número de milisegundos entre cada llamada al método de devolución de llamada definido en el [ `Animation` ](xref:Xamarin.Forms.Animation) constructor
- El cuarto argumento (*longitud*) indica la duración de la animación, en milisegundos.
- El quinto argumento (*aceleración*) define la función de aceleración que se usará en la animación. Como alternativa, la función de aceleración se puede especificar como argumento a la [ `Animation` ](xref:Xamarin.Forms.Animation) constructor. Para obtener más información acerca de funciones de aceleración, vea [funciones de aceleración](~/xamarin-forms/user-interface/animation/easing.md).
- El sexto argumento (*terminado*) es una devolución de llamada que se ejecutará cuando se haya completado la animación. Esta devolución de llamada toma dos argumentos, con el primer argumento que indica un valor final y el segundo argumento es un `bool` que se establece en `true` si se ha cancelado la animación. Como alternativa, el *terminado* devolución de llamada se puede especificar como argumento a la [ `Animation` ](xref:Xamarin.Forms.Animation) constructor. Sin embargo, con una animación única, si *terminado* las devoluciones de llamada se especifican tanto en el `Animation` constructor y el `Commit` método, solo la devolución de llamada especificado en el `Commit` se ejecutará el método.
- El séptimo argumento (*repita*) es una devolución de llamada que permite que la animación se repita. Se llama al final de la animación y devolver `true` indica que se debe repetir la animación.

El efecto general consiste en crear una animación que aumenta la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [ `Image` ](xref:Xamarin.Forms.Image) de 1 a 2, superior a 2 segundos (2.000 milisegundos), mediante el [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) función de aceleración. Cada vez que finaliza la animación, su `Scale` propiedad se restablece en 1 y se repite la animación.

> [!NOTE]
> Las animaciones simultáneas, que se ejecutan de forma independiente entre sí se pueden construir mediante la creación de un `Animation` para cada animación de objetos y, a continuación, llamar a la `Commit` método en cada animación.

<a name="child" />

### <a name="child-animations"></a>Animaciones secundarias

El [ `Animation` ](xref:Xamarin.Forms.Animation) clase también admite animaciones secundarias, que implica la creación de un `Animation` objeto qué Sí `Animation` se agregan objetos. Esto permite una serie de animaciones que se ejecutará y se sincronizan. En el ejemplo de código siguiente se muestra cómo crear y ejecutar animaciones secundarias:

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Como alternativa, el ejemplo de código se puede escribir más concisa, como se muestra en el ejemplo de código siguiente:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

En ambos ejemplos de código, un elemento primario [ `Animation` ](xref:Xamarin.Forms.Animation) se crea el objeto, a la que adicionales `Animation` , a continuación, se agregan objetos. Los dos primeros argumentos para el [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) método especifique cuándo debe comenzar y finalizar la animación secundarios. Los valores de argumento deben estar comprendido entre 0 y 1 y representar el período relativo dentro de la animación principal que se activará la animación secundario especificado. Por lo tanto, en este ejemplo el `scaleUpAnimation` estará activa durante la primera mitad de la animación, la `scaleDownAnimation` estará activa para la segunda mitad de la animación y el `rotateAnimation` estará activa durante toda la duración.

El efecto general es que la animación se produce más de 4 segundos (de 4000 milisegundos). El `scaleUpAnimation` anima la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad de 1 a 2, más de 2 segundos. El `scaleDownAnimation` luego anima la `Scale` propiedad de 2 a 1, más de 2 segundos. Mientras se lleva a cabo ambas animaciones de escalado, la `rotateAnimation` anima la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propiedad comprendido entre 0 y 360, más de 4 segundos. Tenga en cuenta que las animaciones de escalado también usar funciones de aceleración. El [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) función de aceleración hace que el [ `Image` ](xref:Xamarin.Forms.Image) reducir inicialmente antes de obtener mayor y el [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) función de aceleración hace que la `Image` sea menor que su tamaño real hacia el final de la animación completa.

Hay varias diferencias entre una [ `Animation` ](xref:Xamarin.Forms.Animation) objeto que usa animaciones secundarias y que no:

- Al usar animaciones secundarias, el *terminado* devolución de llamada en una animación secundaria indica cuando se haya completado el elemento secundario y el *terminado* devolución de llamada se pasa a la `Commit` método indica cuándo el ha completado la animación completa.
- Al usar animaciones secundarias, devolver `true` desde el *repita* devolución de llamada en el `Commit` método no hará que la animación se repita, pero la animación se seguirá ejecutando sin los nuevos valores.
- Al incluir una función de aceleración en el `Commit` método y la función de aceleración devuelve un valor mayor que 1, se terminará la animación. Si la función de aceleración devuelve un valor menor que 0, el valor se fija en 0. Para utilizar una función de entradas y salidas lenta que devuelve un valor menor que 0 o mayor que 1, debe especificar en una de las animaciones secundarias, en lugar de en el `Commit` método.

El [ `Animation` ](xref:Xamarin.Forms.Animation) también incluye la clase [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) métodos que pueden usarse para agregar animaciones secundarias a un elemento primario `Animation` objeto. Sin embargo, sus *comenzar* y *finalizar* valores de argumento no se restringen a 0 a 1, pero solo esa parte de la animación de secundarios que se corresponde con un intervalo de 0 a 1 estará activa. Por ejemplo, si un `WithConcurrent` llamada al método define una animación secundaria que tiene como destino un [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propiedad desde 1 a 6, pero con *comenzar* y *finalizar* valores de -2 y 3, el *comenzar* valor-2 corresponde a un `Scale` valor 1 y el *finalizar* valor 3 corresponde a un `Scale` valor de 6. Dado que los valores fuera del intervalo de 0 y 1 no reproducción ninguna parte de una animación, la `Scale` propiedad solo se pueden animar de 3 a 6.

## <a name="canceling-an-animation"></a>Cancelación de una animación

Una aplicación puede cancelar una animación con una llamada a la [ `AbortAnimation` ](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) método de extensión, como se muestra en el ejemplo de código siguiente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Tenga en cuenta que las animaciones se identifican mediante una combinación de propietario de la animación y el nombre de la animación. Por lo tanto, el propietario y el nombre especifican al ejecutar la animación debe especificarse para cancelar la animación. Por lo tanto, el ejemplo de código inmediatamente cancelará la animación denominada `SimpleAnimation` que sea propiedad de la página.

## <a name="creating-a-custom-animation"></a>Creación de una animación personalizada

Los ejemplos aquí mostrados hasta ahora han demostrado que las animaciones que podrían obtenerse igualmente con los métodos en el [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase. Sin embargo, la ventaja de la [ `Animation` ](xref:Xamarin.Forms.Animation) es de clase que tiene acceso al método de devolución de llamada, que se ejecuta cuando cambia el valor animado. Esto permite que la devolución de llamada implementar cualquier animación deseado. Por ejemplo, en el ejemplo de código siguiente se anima la [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) propiedad de una página estableciendo en [ `Color` ](xref:Xamarin.Forms.Color) valores creados por la [ `Color.FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))método con valores de matiz comprendida entre 0 y 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

La animación resultante proporciona la apariencia de avanzar el fondo de página a través de los colores del arco iris.

Para obtener más ejemplos de creación de animaciones complejas, incluidas una animación de curva de Bézier, consulte [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) de [Creating Mobile Apps with Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Creación de un método de extensión de animación personalizada

Los métodos de extensión en el [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) clase animar una propiedad de su valor actual en un valor especificado. Esto dificulta crear, por ejemplo, un `ColorTo` método de animación que se puede usar para animar un color en un valor a otro, porque:

- La única [ `Color` ](xref:Xamarin.Forms.Color) propiedad definida por el [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) clase es [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), lo que no siempre es el deseado `Color` propiedad se va a animar.
- A menudo el valor actual de un [ `Color` ](xref:Xamarin.Forms.Color) propiedad es [ `Color.Default` ](xref:Xamarin.Forms.Color.Default), lo que no es un color real y que no se puede usar en los cálculos de interpolación.

La solución a este problema es que no la `ColorTo` como destino un determinado método [ `Color` ](xref:Xamarin.Forms.Color) propiedad. En su lugar, puede escribirse con un método de devolución de llamada que pasa el interpolada `Color` valor al llamador. Además, el método tomará el inicio y finalización `Color` argumentos.

El `ColorTo` método puede implementarse como un método de extensión que usa el [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método en el [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) clase para proporcionar su funcionalidad. Esto es porque el `Animate` método puede utilizarse para las propiedades de destino que no son de tipo `double`, como se muestra en el ejemplo de código siguiente:

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

El [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método requiere un *transformar* argumento, que es un método de devolución de llamada. La entrada para esta devolución de llamada es siempre un `double` comprendida entre 0 y 1. Por lo tanto, el `ColorTo` método define su propia transformación `Func` que acepta un `double` comprendida entre 0 y 1 y que devuelve un [ `Color` ](xref:Xamarin.Forms.Color) valor correspondiente a ese valor. El `Color` valor se calcula mediante la interpolación de la [ `R` ](xref:Xamarin.Forms.Color.R), [ `G` ](xref:Xamarin.Forms.Color.G), [ `B` ](xref:Xamarin.Forms.Color.B), y [ `A` ](xref:Xamarin.Forms.Color.A) los valores de los dos proporcionados `Color` argumentos. El `Color` valor, a continuación, se pasa al método de devolución de llamada para la aplicación a una propiedad determinada.

Este enfoque permite la `ColorTo` método animar cualquiera [ `Color` ](xref:Xamarin.Forms.Color) propiedad, como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

En este ejemplo de código, el `ColorTo` método anima la [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) y [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) las propiedades de un [ `Label` ](xref:Xamarin.Forms.Label), el `BackgroundColor`propiedad de una página y el [ `Color` ](xref:Xamarin.Forms.BoxView.Color) propiedad de un [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar el [ `Animation` ](xref:Xamarin.Forms.Animation) clase para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan las propiedades que no se animación la animación existente métodos. La `Animation` clase es el bloque de creación de todas las animaciones de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Animaciones personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [Animación](xref:Xamarin.Forms.Animation)
- [AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
