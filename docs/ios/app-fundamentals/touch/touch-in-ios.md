---
title: Eventos táctiles y gestos en Xamarin. iOS
description: En este documento se describe cómo trabajar con eventos táctiles, multitáctiles, gestos, varios gestos y gestos personalizados en aplicaciones de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 0fe6b0b46035ac61d4aaddccb585276a80337202
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928814"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Eventos táctiles y gestos en Xamarin. iOS

Es importante comprender los eventos táctiles y las API táctiles en una aplicación de iOS, ya que son esenciales para todas las interacciones físicas con el dispositivo. Todas las interacciones táctiles implican un `UITouch` objeto. En este artículo aprenderá a usar la `UITouch` clase y sus API para admitir la funcionalidad táctil. Más adelante, ampliaremos nuestros conocimientos para obtener información sobre cómo admitir movimientos.

## <a name="enabling-touch"></a>Habilitar Touch

Los controles de `UIKit` , que son subclases de UIControl, dependen de la interacción del usuario de que tienen movimientos integrados en UIKit y, por lo tanto, no es necesario habilitar Touch. Ya está habilitada.

Sin embargo, muchas de las vistas de no `UIKit` tienen la función Touch habilitada de forma predeterminada. Hay dos maneras de habilitar la función táctil en un control. La primera consiste en activar la casilla interacción del usuario habilitada en el panel de propiedades del diseñador de iOS, tal como se muestra en la siguiente captura de pantalla:

 [![Active la casilla interacción con el usuario habilitada en el panel de propiedades del diseñador de iOS.](touch-in-ios-images/image1.png)](touch-in-ios-images/image1.png#lightbox)

También se puede usar un controlador para establecer la `UserInteractionEnabled` propiedad en true en una `UIView` clase. Esto es necesario si la interfaz de usuario se crea en el código.

La siguiente línea de código es un ejemplo:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventos de funciones táctiles

Hay tres fases de toque que se producen cuando el usuario toca la pantalla, mueve el dedo o quita el dedo. Estos métodos se definen en `UIResponder` , que es la clase base para UIView. iOS invalidará los métodos asociados en `UIView` y `UIViewController` para controlar el toque:

- `TouchesBegan`: Se llama a este método cuando se toca la pantalla por primera vez.
- `TouchesMoved`: Se llama a este método cuando la ubicación de la función Touch cambia cuando el usuario está deslizando el dedo por la pantalla.
- `TouchesEnded`o `TouchesCancelled` – `TouchesEnded` se llama cuando los dedos del usuario se levantan de la pantalla.  `TouchesCancelled`se llama a este método si iOS cancela el toque; por ejemplo, si un usuario desliza el dedo desde un botón para cancelar una imprenta.

Los eventos Touch viajan de forma recursiva a través de la pila de UIViews para comprobar si el evento Touch está dentro de los límites de un objeto de vista. Esto se suele denominar _prueba de posicionamiento_. En primer lugar, se llamará en el nivel superior `UIView` o `UIViewController` y, a continuación, se llamará en `UIView` y `UIViewControllers` debajo de ellos en la jerarquía de vistas.

Se `UITouch` creará un objeto cada vez que el usuario toque la pantalla. El `UITouch` objeto incluye datos sobre el toque, como cuando se produjo el toque, donde se produjo, si el toque fue un dedo, etc. Los eventos Touch pasan una propiedad de toques, `NSSet` que contiene uno o más toques. Podemos usar esta propiedad para obtener una referencia a una función táctil y determinar la respuesta de la aplicación.

Las clases que invalidan uno de los eventos Touch deben llamar primero a la implementación base y, a continuación, obtener el `UITouch` objeto asociado al evento. Para obtener una referencia a la primera entrada táctil, llame a la `AnyObject` propiedad y conviértala como `UITouch` tal como se muestra en el ejemplo siguiente:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS reconoce automáticamente los toques rápidos sucesivos de la pantalla y los recopila como una sola pulsación en un solo `UITouch` objeto. Esto hace que la comprobación de una doble TAP sea tan sencilla como la comprobación de la `TapCount` propiedad, como se muestra en el código siguiente:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>Multitáctil

La funcionalidad multitáctil no está habilitada de forma predeterminada en los controles. La funcionalidad multitáctil puede estar habilitada en el diseñador de iOS, tal como se muestra en la siguiente captura de pantalla:

 [![Multitáctil habilitada en el diseñador de iOS](touch-in-ios-images/image2.png)](touch-in-ios-images/image2.png#lightbox)

También es posible establecer la multitáctil mediante programación estableciendo la `MultipleTouchEnabled` propiedad como se muestra en la siguiente línea de código:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Para determinar el número de dedos que toca la pantalla, use la `Count` propiedad en la `UITouch` propiedad:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinar la ubicación táctil

El método `UITouch.LocationInView` devuelve un objeto CGPoint que contiene las coordenadas de la entrada táctil en una vista determinada. Además, se puede probar para ver si esa ubicación está dentro de un control llamando al método `Frame.Contains` . En el fragmento de código siguiente se muestra un ejemplo de esto:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ahora que estamos familiarizados con los eventos Touch en iOS, vamos a obtener información sobre los reconocedores de gestos.

## <a name="gesture-recognizers"></a>Reconocedores de gestos

Los reconocedores de gestos pueden simplificar en gran medida y reducir el esfuerzo de programación para permitir la interacción táctil en una aplicación. los reconocedores de gestos de iOS agregan una serie de eventos táctiles a un único evento Touch.

Xamarin. iOS proporciona la clase `UIGestureRecognizer` como una clase base para los siguientes reconocedores de gestos integrados:

- *UITapGestureRecognizer* : es para uno o más grifos.
- *UIPinchGestureRecognizer* : el contacto y la expansión de los dedos.
- *UIPanGestureRecognizer* : movimiento panorámico o arrastre.
- *UISwipeGestureRecognizer* : desliza el dedo en cualquier dirección.
- *UIRotationGestureRecognizer* : rotación de dos dedos hacia la derecha o hacia la izquierda.
- *UILongPressGestureRecognizer* : mantenga presionado, que a veces se conoce como un clic largo o largo.

El patrón básico para usar un reconocedor de gestos es el siguiente:

1. **Crear instancias del reconocedor de gestos** : en primer lugar, cree una instancia de una `UIGestureRecognizer` subclase. El objeto al que se crea una instancia se asociará con una vista y se recolectará como elemento no utilizado cuando se elimine la vista. No es necesario crear esta vista como una variable de nivel de clase.
1. **Configurar los valores de gestos** : el siguiente paso consiste en configurar el reconocedor de gestos. Consulte la documentación de Xamarin sobre `UIGestureRecognizer` y sus subclases para obtener una lista de las propiedades que se pueden establecer para controlar el comportamiento de una `UIGestureRecognizer` instancia.
1. **Configurar el destino** : debido a su herencia de Objective-C, Xamarin. iOS no genera eventos cuando un reconocedor de gestos coincide con un gesto.  `UIGestureRecognizer`tiene un método, `AddTarget` que puede aceptar un delegado anónimo o un selector de Objective-C con el código que se va a ejecutar cuando el reconocedor de gestos realiza una coincidencia.
1. **Habilitar el reconocedor de gestos** : al igual que con los eventos táctiles, los gestos solo se reconocen si las interacciones táctiles están habilitadas.
1. **Agregar el reconocedor de gestos a la vista** : el paso final consiste en agregar el gesto a una vista mediante una llamada a `View.AddGestureRecognizer` y pasarle un objeto de reconocedor de gestos.

Consulte los [ejemplos del reconocedor de gestos](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) para obtener más información sobre cómo implementarlos en el código.

Cuando se llama al destino del gesto, se le pasará una referencia al gesto que se ha producido. Esto permite que el destino del gesto obtenga información sobre el gesto que se ha producido. La extensión de la información disponible depende del tipo de reconocedor de gestos que se usó. Consulte la documentación de Xamarin para obtener información sobre los datos disponibles para cada `UIGestureRecognizer` subclase.

Es importante recordar que una vez que se ha agregado un reconocedor de gestos a una vista, la vista (y las vistas que aparecen debajo) no recibirá ningún evento de toque. Para permitir los eventos Touch simultáneamente con gestos, la `CancelsTouchesInView` propiedad debe establecerse en false, como se muestra en el código siguiente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Cada `UIGestureRecognizer` una tiene una propiedad de estado que proporciona información importante sobre el estado del reconocedor de gestos. Cada vez que cambia el valor de esta propiedad, iOS llamará al método de suscripción asignándole una actualización. Si un reconocedor de gestos personalizado nunca actualiza la propiedad de estado, nunca se llama al suscriptor, lo que representa el reconocedor de gestos inútil.

Los gestos se pueden resumir como uno de dos tipos:

1. *Discreta* : estos gestos solo se activan la primera vez que se reconocen.
1. *Continuo* : estos gestos continúan activando siempre que se reconozcan.

Los reconocedores de gestos existen en uno de los siguientes Estados:

- *Posible* : este es el estado inicial de todos los reconocedores de gestos. Este es el valor predeterminado de la propiedad State.
- *Iniciado* : cuando se reconoce por primera vez un gesto continuo, el estado se establece en iniciado. Esto permite a las suscripciones diferenciar entre el momento en que se inicia el reconocimiento de gestos y el momento en que se cambia.
- *Cambiado* : después de que haya comenzado un gesto continuo, pero que no haya finalizado, el estado se establecerá en cambiado cada vez que una entrada táctil se mueva o cambie, siempre y cuando se encuentre dentro de los parámetros esperados del gesto.
- *Cancelado* : este estado se establecerá si el reconocedor pasó de empezar a cambiar y, a continuación, los toques cambiaron de forma que ya no se ajuste al patrón del gesto.
- *Reconocido* : el estado se establecerá cuando el reconocedor de gestos coincida con un conjunto de toques y informará al suscriptor de que el gesto ha finalizado.
- *Finalizado* : se trata de un alias para el Estado reconocido.
- *Failed* : cuando el reconocedor de gestos ya no puede coincidir con los toques en los que está escuchando, el estado cambiará a failed.

Xamarin. iOS representa estos valores en la `UIGestureRecognizerState` enumeración.

## <a name="working-with-multiple-gestures"></a>Trabajar con varios gestos

De forma predeterminada, iOS no permite que los gestos predeterminados se ejecuten simultáneamente. En su lugar, cada reconocedor de gestos recibirá eventos táctiles en un orden no determinista. El siguiente fragmento de código ilustra cómo hacer que un reconocedor de gestos se ejecute simultáneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

También es posible deshabilitar un gesto en iOS. Hay dos propiedades de delegado que permiten que un reconocedor de gestos examine el estado de una aplicación y los eventos táctiles actuales, para tomar decisiones sobre cómo y si se debe reconocer un gesto. Los dos eventos son:

1. *ShouldReceiveTouch* : este delegado se llama justo antes de que se pase un evento Touch al reconocedor de gestos y proporciona una oportunidad para examinar los toques y decidir qué toques controlará el reconocedor de gestos.
1. *ShouldBegin* : se llama cuando un reconocedor intenta cambiar el estado de posible a algún otro Estado. Si devuelve false, se forzará el cambio de estado del reconocedor de gestos a failed.

Puede invalidar estos métodos con un delegado fuertemente tipado `UIGestureRecognizerDelegate` , un delegado débil o un enlace a través de la sintaxis del controlador de eventos, como se muestra en el siguiente fragmento de código:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Por último, es posible poner en cola un reconocedor de gestos para que solo se realice correctamente si se produce un error en otro reconocedor de gestos. Por ejemplo, un solo reconocedor de gestos de TAP solo debe tener éxito cuando se produce un error en el reconocedor de gestos de doble punteo. En el fragmento de código siguiente se muestra un ejemplo de esto:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Crear un gesto personalizado

Aunque iOS proporciona algunos reconocedores de gestos predeterminados, puede que sea necesario crear reconocedores de gestos personalizados en ciertos casos. La creación de un reconocedor de gestos personalizado implica los siguientes pasos:

1. Subclase `UIGestureRecognizer` .
1. Invalide los métodos de evento Touch adecuados.
1. Propaga el estado de reconocimiento a través de la propiedad de estado de la clase base.

Un ejemplo práctico de esto se explicará en el tutorial [uso de Touch in iOS](ios-touch-walkthrough.md) .
