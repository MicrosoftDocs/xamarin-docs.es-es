---
title: Animaciones personalizadas enXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4fb9c94c39823e4ce6d60be6b9dbef1294321a63
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137233"
---
# <a name="custom-animations-in-xamarinforms"></a>Animaciones personalizadas enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La clase Animation es el bloque de creación de todas las Xamarin.Forms animaciones, con los métodos de extensión de la clase ViewExtensions que crea uno o varios objetos Animation. En este artículo se muestra cómo usar la clase Animation para crear y cancelar animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animadas por los métodos de animación existentes._

Se debe especificar una serie de parámetros al crear un `Animation` objeto, incluidos los valores inicial y final de la propiedad que se está animando, y una devolución de llamada que cambia el valor de la propiedad. Un `Animation` objeto también puede mantener una colección de animaciones secundarias que se pueden ejecutar y sincronizar. Para obtener más información, vea [animaciones secundarias](#child).

La ejecución de una animación creada con la [`Animation`](xref:Xamarin.Forms.Animation) clase, que puede incluir o no las animaciones secundarias, se consigue mediante una llamada a [ `Commit` ] (XREF: Xamarin.Forms . Animation. Commit () Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Método de aceleración, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean})). Este método especifica la duración de la animación y, entre otros elementos, una devolución de llamada que controla si se debe repetir la animación.

Además, la [`Animation`](xref:Xamarin.Forms.Animation) clase tiene una `IsEnabled` propiedad que se puede examinar para determinar si el sistema operativo ha deshabilitado las animaciones, como cuando se activa el modo de ahorro de energía.

## <a name="create-an-animation"></a>Creación de una animación

Al crear un [`Animation`](xref:Xamarin.Forms.Animation) objeto, normalmente se requiere un mínimo de tres parámetros, como se muestra en el ejemplo de código siguiente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Este código define una animación de la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de una [`Image`](xref:Xamarin.Forms.Image) instancia de un valor de 1 a un valor de 2. El valor animado, derivado de Xamarin.Forms , se pasa a la devolución de llamada especificada como primer argumento, donde se usa para cambiar el valor de la `Scale` propiedad.

La animación se inicia con una llamada a [ `Commit` ] (XREF: Xamarin.Forms . Animation. Commit () Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Método de aceleración, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean})), como se muestra en el ejemplo de código siguiente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Tenga en cuenta que [ `Commit` ] (XREF: Xamarin.Forms . Animation. Commit () Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . El método de aceleración, System. Action {System. Double, System. Boolean}, System. Func {System. Boolean})) no devuelve un `Task` objeto. En su lugar, las notificaciones se proporcionan a través de métodos de devolución de llamada.

Los siguientes argumentos se especifican en el `Commit` método:

- El primer argumento (*propietario*) identifica el propietario de la animación. Puede ser el elemento visual en el que se aplica la animación u otro elemento visual, como la página.
- El segundo argumento (*Name*) identifica la animación con un nombre. El nombre se combina con el propietario para identificar de forma única la animación. Esta identificación única se puede usar para determinar si la animación se está ejecutando ([ `AnimationIsRunning` ] (XREF: Xamarin.Forms . AnimationExtensions. AnimationIsRunning ( Xamarin.Forms . IAnimatable, System. String))) o para cancelarla ([ `AbortAnimation` ] (XREF: Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . IAnimatable, System. String))).
- El tercer argumento (*Rate*) indica el número de milisegundos entre cada llamada al método de devolución de llamada definido en el [`Animation`](xref:Xamarin.Forms.Animation) constructor.
- El cuarto argumento (*length*) indica la duración de la animación, en milisegundos.
- El quinto argumento (*aceleración*) define la función de aceleración que se va a usar en la animación. Como alternativa, se puede especificar la función de aceleración como argumento para el [`Animation`](xref:Xamarin.Forms.Animation) constructor. Para obtener más información sobre las funciones de aceleración, consulte [funciones de aceleración](~/xamarin-forms/user-interface/animation/easing.md).
- El sexto argumento (*finalizado*) es una devolución de llamada que se ejecutará cuando se complete la animación. Esta devolución de llamada toma dos argumentos, con el primer argumento que indica un valor final, y el segundo argumento es un `bool` que se establece en `true` si se canceló la animación. Como alternativa, la devolución de llamada *finalizada* se puede especificar como argumento para el [`Animation`](xref:Xamarin.Forms.Animation) constructor. Sin embargo, con una sola animación, si se especifican devoluciones de llamada *terminadas* tanto en el `Animation` constructor como en el `Commit` método, solo se ejecutará la devolución de llamada especificada en el `Commit` método.
- El séptimo argumento (*REPEAT*) es una devolución de llamada que permite repetir la animación. Se llama al final de la animación y `true` la devolución indica que se debe repetir la animación.

El efecto general es crear una animación que aumente la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [`Image`](xref:Xamarin.Forms.Image) de 1 a 2, más de 2 segundos (2000 milisegundos), mediante la [`Linear`](xref:Xamarin.Forms.Easing.Linear) función de aceleración. Cada vez que se completa la animación, su `Scale` propiedad se restablece en 1 y la animación se repite.

> [!NOTE]
> Las animaciones simultáneas, que se ejecutan de forma independiente entre sí, se pueden construir creando un `Animation` objeto para cada animación y, a continuación, llamando al `Commit` método en cada animación.

<a name="child" />

### <a name="child-animations"></a>Animaciones secundarias

La [`Animation`](xref:Xamarin.Forms.Animation) clase también admite animaciones secundarias, lo que implica la creación `Animation` de un objeto al que `Animation` se agregan otros objetos. Esto permite ejecutar y sincronizar una serie de animaciones. En el ejemplo de código siguiente se muestra cómo crear y ejecutar animaciones secundarias:

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

Como alternativa, el ejemplo de código se puede escribir de forma más concisa, como se muestra en el ejemplo de código siguiente:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

En ambos ejemplos de código, [`Animation`](xref:Xamarin.Forms.Animation) se crea un objeto primario, al que `Animation` se agregan los objetos adicionales. Los dos primeros argumentos para [ `Add` ] (XREF: Xamarin.Forms . Animation. Add (System. Double, System. Double, Xamarin.Forms . Animation)) especifique Cuándo debe comenzar y finalizar la animación secundaria. Los valores de argumento deben estar entre 0 y 1, y representan el período relativo dentro de la animación primaria que la animación secundaria especificada estará activa. Por lo tanto, en este ejemplo, estará `scaleUpAnimation` activo para la primera mitad de la animación, estará `scaleDownAnimation` activo para la segunda mitad de la animación y el `rotateAnimation` estará activo durante toda la duración.

El efecto general es que la animación se produce durante 4 segundos (4000 milisegundos). `scaleUpAnimation`Anima la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de 1 a 2, en más de 2 segundos. `scaleDownAnimation`A continuación, anima la `Scale` propiedad de 2 a 1, en más de 2 segundos. Mientras se producen ambas animaciones de escala, `rotateAnimation` anima la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de 0 a 360, en 4 segundos. Tenga en cuenta que las animaciones de escalado también usan funciones de aceleración. La [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) función de entradas y salidas lentas hace que [`Image`](xref:Xamarin.Forms.Image) se reduzca inicialmente antes de aumentar de tamaño, y la [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) función de aceleración hace que se `Image` convierta en menor que el tamaño real hacia el final de la animación completa.

Hay una serie de diferencias entre un [`Animation`](xref:Xamarin.Forms.Animation) objeto que utiliza animaciones secundarias y otra que no:

- Al usar animaciones secundarias, la devolución de llamada *finalizada* en una animación secundaria indica cuándo se ha completado el elemento secundario y la devolución de llamada *finalizada* pasada al `Commit` método indica cuándo se ha completado toda la animación.
- Al usar animaciones secundarias, la `true` devolución de la devolución de llamada de *repetición* en el `Commit` método no hará que se repita la animación, pero la animación continuará ejecutándose sin nuevos valores.
- Al incluir una función de aceleración en el `Commit` método, y la función de aceleración devuelve un valor mayor que 1, se terminará la animación. Si la función de aceleración devuelve un valor menor que 0, el valor se fija en 0. Para usar una función de entradas y salidas lentas que devuelva un valor menor que 0 o mayor que 1, debe especificarse en una de las animaciones secundarias, en lugar de en el `Commit` método.

La [`Animation`](xref:Xamarin.Forms.Animation) clase también incluye [ `WithConcurrent` ] (XREF: Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Métodos Animation, System. Double, System. Double)) que se pueden usar para agregar animaciones secundarias a un `Animation` objeto primario. Sin embargo, sus valores de argumento *Begin* y *Finish* no están restringidos a 0 a 1, pero solo estarán activos las partes de la animación secundaria que correspondan a un intervalo de 0 a 1. Por ejemplo, si una `WithConcurrent` llamada al método define una animación secundaria que tiene como destino una [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de 1 a 6, pero con los valores de *Inicio* y *fin* de-2 y 3, el valor *inicial* de-2 corresponde a un `Scale` valor de 1 y el valor de *final* de 3 corresponde a un `Scale` valor de 6. Dado que los valores que están fuera del intervalo de 0 y 1 no juegan ninguna parte en una animación, la `Scale` propiedad solo se animará de 3 a 6.

## <a name="cancel-an-animation"></a>Cancelar una animación

Una aplicación puede cancelar una animación con una llamada a [ `AbortAnimation` ] (XREF: Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . IAnimatable, System. String)), como se muestra en el ejemplo de código siguiente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Tenga en cuenta que las animaciones se identifican de forma única mediante una combinación del propietario de la animación y el nombre de la animación. Por lo tanto, se debe especificar el propietario y el nombre especificados cuando se ejecuta la animación para cancelar la animación. Por lo tanto, el ejemplo de código cancelará inmediatamente la animación denominada `SimpleAnimation` que es propiedad de la página.

## <a name="create-a-custom-animation"></a>Crear una animación personalizada

Los ejemplos que se muestran aquí hasta ahora han demostrado animaciones que se podrían lograr igualmente con los métodos de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase. Sin embargo, la ventaja de la [`Animation`](xref:Xamarin.Forms.Animation) clase es que tiene acceso al método de devolución de llamada, que se ejecuta cuando cambia el valor animado. Esto permite que la devolución de llamada implemente cualquier animación deseada. Por ejemplo, en el ejemplo de código siguiente se anima la [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) propiedad de una página estableciéndolo en [`Color`](xref:Xamarin.Forms.Color) valores creados por el [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) método, con valores de matiz comprendidos entre 0 y 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

La animación resultante proporciona la apariencia de avanzar el fondo de la página a través de los colores del arco iris.

Para obtener más ejemplos de creación de animaciones complejas, incluida una animación de curva de Bézier, vea el [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) de [creación de Mobile Apps Xamarin.Forms con ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Crear un método de extensión de animación personalizado

Los métodos de extensión de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase animan una propiedad desde su valor actual hasta un valor especificado. Esto hace que sea difícil crear, por ejemplo, un `ColorTo` método de animación que se puede usar para animar un color de un valor a otro, porque:

- La única [`Color`](xref:Xamarin.Forms.Color) propiedad definida por la [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase es [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) , que no siempre es la `Color` propiedad deseada que se va a animar.
- A menudo, el valor actual de una [`Color`](xref:Xamarin.Forms.Color) propiedad es [`Color.Default`](xref:Xamarin.Forms.Color.Default) , que no es un color real, y que no se puede usar en los cálculos de interpolación.

La solución a este problema es no tener el `ColorTo` destino del método en una [`Color`](xref:Xamarin.Forms.Color) propiedad determinada. En su lugar, se puede escribir con un método de devolución de llamada que devuelve el valor interpolado `Color` al llamador. Además, el método tomará los argumentos de inicio y fin `Color` .

El `ColorTo` método se puede implementar como un método de extensión que utiliza el [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) método en la [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) clase para proporcionar su funcionalidad. Esto se debe a que el `Animate` método se puede usar para tener como destino propiedades que no son de tipo `double` , tal y como se muestra en el ejemplo de código siguiente:

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

El [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) método requiere un argumento de *transformación* , que es un método de devolución de llamada. La entrada a esta devolución de llamada siempre es un `double` intervalo de 0 a 1. Por lo tanto, el `ColorTo` método define su propia transformación `Func` que acepta un que `double` oscila entre 0 y 1, y que devuelve un [`Color`](xref:Xamarin.Forms.Color) valor correspondiente a ese valor. El `Color` valor se calcula interpolando los [`R`](xref:Xamarin.Forms.Color.R) valores, [`G`](xref:Xamarin.Forms.Color.G) , [`B`](xref:Xamarin.Forms.Color.B) y [`A`](xref:Xamarin.Forms.Color.A) de los dos argumentos proporcionados `Color` . `Color`Después, el valor se pasa al método de devolución de llamada para la aplicación a una propiedad determinada.

Este enfoque permite al `ColorTo` método animar cualquier [`Color`](xref:Xamarin.Forms.Color) propiedad, tal y como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

En este ejemplo de código, el `ColorTo` método anima las [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) propiedades y de un [`Label`](xref:Xamarin.Forms.Label) , la `BackgroundColor` propiedad de una página y la [`Color`](xref:Xamarin.Forms.BoxView.Color) propiedad de un [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="related-links"></a>Vínculos relacionados

- [Animaciones personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [Animation API](xref:Xamarin.Forms.Animation)
- [API de AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
