---
title: Animaciones personalizadas en Xamarin.Forms
description: En este artículo se muestra cómo usar la clase de animación de Xamarin.FOrms para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan las propiedades que no se animación mediante los métodos de animación existentes.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
ms.openlocfilehash: 405d7990b622b890aa3d66bd632662f086441666
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635762"
---
# <a name="custom-animations-in-xamarinforms"></a>Animaciones personalizadas en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La clase Animation es el bloque de creación de todas las animaciones de Xamarin. Forms, con los métodos de extensión de la clase ViewExtensions que crea uno o varios objetos Animation. En este artículo se muestra cómo usar la clase Animation para crear y cancelar animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animadas por los métodos de animación existentes._

Se debe especificar una serie de parámetros al crear un `Animation` objeto, incluidos los valores de inicio y fin de la propiedad que se está animando, y una devolución de llamada que cambia el valor de la propiedad. Un objeto `Animation` también puede mantener una colección de animaciones secundarias que se pueden ejecutar y sincronizar. Para obtener más información, vea [animaciones secundarias](#child).

La ejecución de una animación creada con la clase [`Animation`](xref:Xamarin.Forms.Animation) , que puede incluir o no animaciones secundarias, se consigue mediante una llamada al método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) . Este método especifica la duración de la animación y, entre otros elementos, una devolución de llamada que controla si se debe repetir la animación.

Además, la clase [`Animation`](xref:Xamarin.Forms.Animation) tiene una propiedad `IsEnabled` que se puede examinar para determinar si el sistema operativo ha deshabilitado las animaciones, como cuando se activa el modo de ahorro de energía.

## <a name="create-an-animation"></a>Creación de una animación

Al crear un objeto de [`Animation`](xref:Xamarin.Forms.Animation) , normalmente se requiere un mínimo de tres parámetros, como se muestra en el ejemplo de código siguiente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Este código define una animación de la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de una instancia de [`Image`](xref:Xamarin.Forms.Image) de un valor de 1 a un valor de 2. El valor animado, que se deriva de Xamarin. Forms, se pasa a la devolución de llamada especificada como primer argumento, donde se usa para cambiar el valor de la propiedad `Scale`.

La animación se inicia con una llamada al método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) , como se muestra en el ejemplo de código siguiente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Tenga en cuenta que el método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) no devuelve un objeto `Task`. En su lugar, las notificaciones se proporcionan a través de métodos de devolución de llamada.

Los siguientes argumentos se especifican en el método `Commit`:

- El primer argumento (*propietario*) identifica el propietario de la animación. Esto puede ser el elemento visual en el que se aplica la animación u otro elemento visual, como la página.
- El segundo argumento (*Name*) identifica la animación con un nombre. El nombre se combina con el propietario para identificar de forma única la animación. Esta identificación única se puede usar para determinar si la animación se está ejecutando ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))) o para cancelarla ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- El tercer argumento (*Rate*) indica el número de milisegundos entre cada llamada al método de devolución de llamada definido en el constructor [`Animation`](xref:Xamarin.Forms.Animation)
- El cuarto argumento (*length*) indica la duración de la animación, en milisegundos.
- El quinto argumento (*aceleración*) define la función de aceleración que se va a usar en la animación. Como alternativa, se puede especificar la función de aceleración como argumento para el constructor de [`Animation`](xref:Xamarin.Forms.Animation) . Para obtener más información sobre las funciones de aceleración, consulte [funciones de aceleración](~/xamarin-forms/user-interface/animation/easing.md).
- El sexto argumento (*finalizado*) es una devolución de llamada que se ejecutará cuando se complete la animación. Esta devolución de llamada toma dos argumentos, con el primer argumento que indica un valor final, y el segundo argumento es un `bool` que se establece en `true` si se canceló la animación. Como alternativa, la devolución de llamada *finalizada* se puede especificar como argumento para el constructor de [`Animation`](xref:Xamarin.Forms.Animation) . Sin embargo, con una sola animación, si las devoluciones de llamada *finalizadas* se especifican tanto en el constructor `Animation` como en el método `Commit`, solo se ejecutará la devolución de llamada especificada en el método `Commit`.
- El séptimo argumento (*REPEAT*) es una devolución de llamada que permite repetir la animación. Se llama al final de la animación y, al devolver `true`, se indica que se debe repetir la animación.

El efecto general es crear una animación que aumente la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de una [`Image`](xref:Xamarin.Forms.Image) de 1 a 2, en más de 2 segundos (2000 milisegundos), mediante la función de aceleración [`Linear`](xref:Xamarin.Forms.Easing.Linear) . Cada vez que se completa la animación, su propiedad `Scale` se restablece en 1 y la animación se repite.

> [!NOTE]
> Las animaciones simultáneas, que se ejecutan de forma independiente entre sí, se pueden construir creando un `Animation` objeto para cada animación y, a continuación, llamando al método `Commit` en cada animación.

<a name="child" />

### <a name="child-animations"></a>Animaciones secundarias

La clase [`Animation`](xref:Xamarin.Forms.Animation) también admite animaciones secundarias, lo que implica la creación de un objeto `Animation` al que se agregan otros objetos `Animation`. Esto permite una serie de animaciones que se ejecutará y se sincronizan. En el ejemplo de código siguiente se muestra cómo crear y ejecutar animaciones secundarias:

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

En ambos ejemplos de código, se crea un objeto [`Animation`](xref:Xamarin.Forms.Animation) primario, al que se agregan más objetos `Animation`. Los dos primeros argumentos del método [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) especifican Cuándo debe comenzar y finalizar la animación secundaria. Los valores de argumento deben estar comprendido entre 0 y 1 y representar el período relativo dentro de la animación principal que se activará la animación secundario especificado. Por lo tanto, en este ejemplo, el `scaleUpAnimation` estará activo para la primera mitad de la animación, el `scaleDownAnimation` estará activo para la segunda mitad de la animación y el `rotateAnimation` estará activo durante toda la duración.

El efecto general es que la animación se produce más de 4 segundos (de 4000 milisegundos). El `scaleUpAnimation` anima la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de 1 a 2, en más de 2 segundos. A continuación, el `scaleDownAnimation` anima la propiedad `Scale` de 2 a 1, en más de 2 segundos. Mientras se producen ambas animaciones de escala, el `rotateAnimation` anima la propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de 0 a 360, en 4 segundos. Tenga en cuenta que las animaciones de escalado también usar funciones de aceleración. La función de aceleración de [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) hace que el [`Image`](xref:Xamarin.Forms.Image) se reduzca inicialmente antes de aumentar de tamaño y la función de aceleración de [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) hace que el `Image` sea más pequeño que el tamaño real hacia el final de la animación completa.

Hay una serie de diferencias entre un objeto [`Animation`](xref:Xamarin.Forms.Animation) que utiliza animaciones secundarias y otra que no:

- Cuando se usan animaciones secundarias, la devolución de llamada *finalizada* en una animación secundaria indica cuándo se ha completado el elemento secundario y la devolución de llamada *finalizada* pasada al método `Commit` indica cuándo se ha completado toda la animación.
- Al usar animaciones secundarias, la devolución de `true` desde la devolución de llamada de *repetición* en el método `Commit` no hará que se repita la animación, pero la animación continuará ejecutándose sin valores nuevos.
- Cuando se incluye una función de aceleración en el método `Commit` y la función de aceleración devuelve un valor mayor que 1, se terminará la animación. Si la función de aceleración devuelve un valor menor que 0, el valor se fija en 0. Para usar una función de aceleración que devuelva un valor menor que 0 o mayor que 1, debe especificarse en una de las animaciones secundarias, en lugar de en el método `Commit`.

La clase [`Animation`](xref:Xamarin.Forms.Animation) también incluye [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) métodos que se pueden utilizar para agregar animaciones secundarias a un objeto `Animation` primario. Sin embargo, sus valores de argumento *Begin* y *Finish* no están restringidos a 0 a 1, pero solo estarán activos las partes de la animación secundaria que correspondan a un intervalo de 0 a 1. Por ejemplo, si una llamada al método `WithConcurrent` define una animación secundaria que tiene como destino una propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de 1 a 6, pero con los valores de *Inicio* y *fin* de-2 y 3, el valor *inicial* de-2 corresponde a un valor de `Scale` de 1 y el valor de *final* de 3 corresponde a un valor `Scale` de 6. Dado que los valores que están fuera del intervalo de 0 y 1 no juegan ninguna parte en una animación, la propiedad `Scale` solo se animará de 3 a 6.

## <a name="cancel-an-animation"></a>Cancelar una animación

Una aplicación puede cancelar una animación con una llamada al método de extensión [`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) , como se muestra en el ejemplo de código siguiente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Tenga en cuenta que las animaciones se identifican mediante una combinación de propietario de la animación y el nombre de la animación. Por lo tanto, el propietario y el nombre especifican al ejecutar la animación debe especificarse para cancelar la animación. Por lo tanto, el ejemplo de código cancelará inmediatamente la animación denominada `SimpleAnimation` que es propiedad de la página.

## <a name="create-a-custom-animation"></a>Crear una animación personalizada

Los ejemplos que se muestran aquí hasta ahora han demostrado animaciones que se podrían lograr igualmente con los métodos de la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Sin embargo, la ventaja de la clase [`Animation`](xref:Xamarin.Forms.Animation) es que tiene acceso al método de devolución de llamada, que se ejecuta cuando cambia el valor animado. Esto permite que la devolución de llamada implementar cualquier animación deseado. Por ejemplo, en el ejemplo de código siguiente se anima la propiedad [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de una página estableciéndolo en [`Color`](xref:Xamarin.Forms.Color) valores creados por el método [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) , con valores de matiz comprendidos entre 0 y 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

La animación resultante proporciona la apariencia de avanzar el fondo de página a través de los colores del arco iris.

Para obtener más ejemplos de creación de animaciones complejas, incluida una animación de curva de Bézier, vea el [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) sobre [Cómo crear Mobile Apps con Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Crear un método de extensión de animación personalizado

Los métodos de extensión de la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) animan una propiedad desde su valor actual hasta un valor especificado. Esto hace que sea difícil crear, por ejemplo, un método de animación `ColorTo` que se puede usar para animar un color de un valor a otro, porque:

- La única [`Color`](xref:Xamarin.Forms.Color) propiedad definida por la clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) es [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor), que no siempre es la propiedad `Color` deseada que se va a animar.
- A menudo, el valor actual de una propiedad [`Color`](xref:Xamarin.Forms.Color) es [`Color.Default`](xref:Xamarin.Forms.Color.Default), que no es un color real, y que no se puede usar en los cálculos de interpolación.

La solución a este problema es no tener el método `ColorTo` como destino de una propiedad [`Color`](xref:Xamarin.Forms.Color) determinada. En su lugar, se puede escribir con un método de devolución de llamada que vuelva a pasar el valor de `Color` interpolado al llamador. Además, el método tomará los argumentos de `Color` inicial y final.

El método `ColorTo` se puede implementar como un método de extensión que utiliza el método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) de la clase [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) para proporcionar su funcionalidad. Esto se debe a que el método `Animate` se puede usar para tener como destino propiedades que no son de tipo `double`, como se muestra en el ejemplo de código siguiente:

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

El método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) requiere un argumento *Transform* , que es un método de devolución de llamada. La entrada a esta devolución de llamada siempre es un `double` que va de 0 a 1. Por lo tanto, el método `ColorTo` define su propia transformación `Func` que acepta un `double` comprendido entre 0 y 1, y que devuelve un valor [`Color`](xref:Xamarin.Forms.Color) correspondiente a ese valor. El valor de `Color` se calcula interpolando los valores de [`R`](xref:Xamarin.Forms.Color.R), [`G`](xref:Xamarin.Forms.Color.G), [`B`](xref:Xamarin.Forms.Color.B)y [`A`](xref:Xamarin.Forms.Color.A) de los dos argumentos de `Color` proporcionados. A continuación, el valor `Color` se pasa al método de devolución de llamada para la aplicación a una propiedad determinada.

Este enfoque permite al método `ColorTo` animar cualquier propiedad [`Color`](xref:Xamarin.Forms.Color) , tal y como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

En este ejemplo de código, el método `ColorTo` anima las propiedades [`TextColor`](xref:Xamarin.Forms.Label.TextColor) y [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de un [`Label`](xref:Xamarin.Forms.Label), la propiedad `BackgroundColor` de una página y la propiedad [`Color`](xref:Xamarin.Forms.BoxView.Color) de una [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="related-links"></a>Vínculos relacionados

- [Animaciones personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [Animation API](xref:Xamarin.Forms.Animation)
- [API de AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
