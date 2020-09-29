---
title: Animación básica en Xamarin. iOS
description: En este artículo se examina el marco de animación básico, en el que se muestra cómo permite animaciones fluidas y de alto rendimiento en UIKit, así como cómo usarla directamente para el control de animación de nivel inferior.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d25d48421ad9b05925c1fa373ddba600ad3bac2e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431225"
---
# <a name="core-animation-in-xamarinios"></a>Animación básica en Xamarin. iOS

_En este artículo se examina el marco de animación básico, en el que se muestra cómo permite animaciones fluidas y de alto rendimiento en UIKit, así como cómo usarla directamente para el control de animación de nivel inferior._

iOS incluye la [*animación básica*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) para proporcionar compatibilidad con las vistas de la aplicación.
Todas las animaciones ultra suaves de iOS, como el desplazamiento de tablas y el deslizamiento entre las distintas vistas, funcionan así porque se basan en la animación básica internamente.

La animación básica y los marcos de gráficos principales pueden trabajar juntos para crear gráficos 2D animados y atractivos. En realidad, la animación básica también puede transformar gráficos 2D en el espacio 3D, lo que crea experiencias sorprendentes y cinematográficas. Sin embargo, para crear verdaderos gráficos 3D, tendría que usar algo como OpenGL ES o, para juegos, convertirlo en una API como monogame, aunque 3D está fuera del ámbito de este artículo.

<a name="Using_Core_Animation"></a>

## <a name="core-animation"></a>Animación básica

iOS usa el marco de animaciones básicas para crear efectos de animación, como la transición entre vistas, menús deslizantes y efectos de desplazamiento, por nombrar algunos. Hay dos maneras de trabajar con la animación:

- [A través de UIKit](#Using_UIKit_Animation), que incluye animaciones basadas en vistas, así como transiciones animadas entre controladores.
- [A través de la animación básica](#Using_Core_Animation), que se agrega directamente a capas, lo que permite un control más preciso.

<a name="Using_UIKit_Animation"></a>

## <a name="using-uikit-animation"></a>Usar la animación UIKit

UIKit proporciona varias características que facilitan la adición de animaciones a una aplicación. Aunque usa la animación básica internamente, la abstrae para que solo trabaje con vistas y controladores.

En esta sección se describen las características de animación de UIKit, entre las que se incluyen:

- Transiciones entre controladores
- Transiciones entre vistas
- Animación de la propiedad View

### <a name="view-controller-transitions"></a>Transiciones de controladores de vistas

 `UIViewController` proporciona compatibilidad integrada para la transición entre controladores de vista a través del `PresentViewController` método. Cuando `PresentViewController` se usa, la transición al segundo controlador se puede animar opcionalmente.

Por ejemplo, considere una aplicación con dos controladores, donde tocar un botón en el primer controlador llama `PresentViewController` a para mostrar un segundo controlador. Para controlar qué animación de transición se usa para mostrar el segundo controlador, simplemente establezca su [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) propiedad como se muestra a continuación:

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

En este caso `PartialCurl` , se usa una animación, aunque hay varias otras disponibles, entre las que se incluyen:

- `CoverVertical` : Se desliza hacia arriba desde la parte inferior de la pantalla.
- `CrossDissolve` : La vista antigua atenúa & la nueva vista se atenúa.
- `FlipHorizontal` : Un volteo horizontal de derecha a izquierda. En el descartado, la transición se voltea de izquierda a derecha.

Para animar la transición, pase `true` como el segundo argumento a `PresentViewController` :

```csharp
PresentViewController (vc2, true, null);
```

En la captura de pantalla siguiente se muestra el aspecto de la transición para el `PartialCurl` caso:

 ![En esta captura de pantalla se muestra la transición PartialCurl](core-animation-images/06-view-transitions.png)

### <a name="view-transitions"></a>Ver transiciones

Además de las transiciones entre controladores, UIKit también admite la animación de transiciones entre vistas para intercambiar una vista para otra.

Por ejemplo, suponga que tiene un controlador con `UIImageView` , donde el punteo de la imagen debe mostrar un segundo `UIImageView` . Para animar la supervista de la vista de imagen a la transición a la segunda vista de imagen es tan sencilla como llamar a `UIView.Transition` , pasándole `toView` y `fromView` como se muestra a continuación:

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` también toma un `duration` parámetro que controla durante cuánto tiempo se ejecuta la animación, así como [`options`](xref:UIKit.UIViewAnimationOptions) para especificar elementos como la animación que se va a usar y la función de aceleración. Además, puede especificar un controlador de finalización al que se llamará cuando se complete la animación.

En la captura de pantalla siguiente se muestra la transición animada entre las vistas de imagen cuando `TransitionFlipFromTop` se usa:

 ![En esta captura de pantalla se muestra la transición animada entre las vistas de imagen cuando se usa TransitionFlipFromTop](core-animation-images/07-animated-transition.png)

### <a name="view-property-animations"></a>Ver animaciones de propiedades

UIKit admite animar una variedad de propiedades de la `UIView` clase de forma gratuita, entre las que se incluyen:

- Fotograma
- Bounds
- Center
- Alpha
- Transformación
- Color

Estas animaciones se producen implícitamente mediante la especificación de los cambios de propiedad en un `NSAction` delegado que se pasa al `UIView.Animate` método estático. Por ejemplo, el código siguiente anima el punto central de un `UIImageView` :

```csharp
pt = imgView.Center;

UIView.Animate (
  duration: 2, 
  delay: 0, 
  options: UIViewAnimationOptions.CurveEaseInOut | 
    UIViewAnimationOptions.Autoreverse,
  animation: () => {
    imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
      - imgView.Frame.Width / 2, pt.Y);},
  completion: () => {
    imgView.Center = pt; }
);
```

Esto da como resultado una imagen que se anima hacia atrás y hacia delante en la parte superior de la pantalla, como se muestra a continuación:

 ![Imagen que se anima hacia atrás y hacia delante en la parte superior de la pantalla como salida](core-animation-images/08-animate-center.png)

Al igual que con el `Transition` método, `Animate` permite establecer la duración, junto con la función de aceleración. En este ejemplo también se usa la `UIViewAnimationOptions.Autoreverse` opción, que hace que la animación se anime desde el valor hacia el inicial. Sin embargo, el código también establece de `Center` nuevo en su valor inicial en un controlador de finalización. Mientras que una animación está interpolando valores de propiedad a lo largo del tiempo, el valor real del modelo de la propiedad siempre es el valor final que se ha establecido. En este ejemplo, el valor es un punto situado cerca del lado derecho de la supervista. Sin establecer `Center` en el punto inicial, que es donde se completa la animación debido a que `Autoreverse` se establece, la imagen se ajustará al lado derecho una vez finalizada la animación, como se muestra a continuación:

 ![Si no se establece el centro en el punto inicial, la imagen se volverá a ajustar al lado derecho una vez completada la animación.](core-animation-images/09-animation-complete.png)

## <a name="using-core-animation"></a>Usar la animación básica

 `UIView` las animaciones permiten una gran cantidad de capacidad y deben usarse si es posible debido a la facilidad de implementación. Como se mencionó anteriormente, las animaciones de UIView usan el marco de animaciones básicas. Sin embargo, algunas cosas no se pueden realizar con `UIView` animaciones, como la animación de propiedades adicionales que no se pueden animar con una vista o la interpolación a lo largo de una ruta de acceso no lineal. En casos en los que necesite un control más preciso, la animación básica también puede usarse directamente.

### <a name="layers"></a>Capas

Al trabajar con la animación básica, la animación se produce a través de *capas*, que son de tipo `CALayer` . Una capa es conceptualmente similar a una vista en que hay una jerarquía de capas, de forma muy parecida a como hay una jerarquía de vistas. En realidad, las vistas inversas de capas, con la vista que agrega compatibilidad para la interacción con el usuario. Puede tener acceso a la capa de cualquier vista a través de la propiedad de la vista `Layer` . De hecho, el contexto utilizado en `Draw` el método de `UIView` se crea realmente a partir de la capa. Internamente, la capa de respaldo de `UIView` tiene su delegado establecido en la propia vista, que es lo que llama a `Draw` . Por tanto, al dibujar en un `UIView` , realmente está dibujando en su capa.

Las animaciones de capas pueden ser implícitas o explícitas. Las animaciones implícitas son declarativas. Solo tiene que declarar qué propiedades de capa deben cambiar y la animación funciona. Por otro lado, las animaciones explícitas se crean a través de una clase de animación que se agrega a una capa. Las animaciones explícitas permiten un control adicional sobre cómo se crea una animación. En las secciones siguientes se profundiza en animaciones implícitas y explícitas con mayor profundidad.

### <a name="implicit-animations"></a>Animaciones IMPLÍCITAS

Una manera de animar las propiedades de una capa es a través de una animación implícita. `UIView` las animaciones crean animaciones implícitas. Sin embargo, también puede crear animaciones implícitas directamente en una capa.

Por ejemplo, el código siguiente establece un de una capa a `Contents` partir de una imagen, establece un ancho y un color de borde, y agrega la capa como una subcapa de la capa de la vista:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  layer = new CALayer ();
  layer.Bounds = new CGRect (0, 0, 50, 50);
  layer.Position = new CGPoint (50, 50);
  layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
  layer.ContentsGravity = CALayer.GravityResize;
  layer.BorderWidth = 1.5f;
  layer.BorderColor = UIColor.Green.CGColor;

  View.Layer.AddSublayer (layer);
}
```

Para agregar una animación implícita para la capa, solo tiene que ajustar los cambios de propiedad en un `CATransaction` . Esto permite animar las propiedades que no se pueden animar con una animación de vista, como `BorderWidth` y, `BorderColor` como se muestra a continuación:

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);

  CATransaction.Begin ();
  CATransaction.AnimationDuration = 10;
  layer.Position = new CGPoint (50, 400);
  layer.BorderWidth = 5.0f;
  layer.BorderColor = UIColor.Red.CGColor;
  CATransaction.Commit ();
}
```

Este código también anima el de la capa `Position` , que es la ubicación del punto de anclaje de la capa, medido desde la parte superior izquierda de las coordenadas del supernivel. El punto de anclaje de una capa es un punto normalizado en el sistema de coordenadas de la capa.

En la ilustración siguiente se muestra la posición y el punto de anclaje:

 ![En esta ilustración se muestra la posición y el punto de anclaje](core-animation-images/10-postion-anchorpt.png)

Cuando se ejecuta el ejemplo, `Position` y se `BorderWidth` `BorderColor` animan como se muestra en las siguientes capturas de pantallas:

 ![Cuando se ejecuta el ejemplo, la posición, BorderWidth y BorderColor se animan como se muestra](core-animation-images/11-implicit-animation.png)

### <a name="explicit-animations"></a>Animaciones explícitas

Además de las animaciones IMPLÍCITAS, la animación básica incluye una variedad de clases que heredan de `CAAnimation` que permiten encapsular animaciones que se agregan explícitamente a una capa. Estos permiten un control más preciso sobre las animaciones, como la modificación del valor inicial de una animación, la agrupación de animaciones y la especificación de fotogramas clave para permitir trazados no lineales.

En el código siguiente se muestra un ejemplo de una animación explícita utilizando un `CAKeyframeAnimation` para la capa mostrada anteriormente (en la sección de animación implícita):

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);
  
  // get the initial value to start the animation from
  CGPoint fromPt = layer.Position;
  
  /* set the position to coincide with the final animation value
  to prevent it from snapping back to the starting position
  after the animation completes*/
  layer.Position = new CGPoint (200, 300);
  
  // create a path for the animation to follow
  CGPath path = new CGPath ();
  path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
  
  // create a keyframe animation for the position using the path
  CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
  animPosition.Path = path;
  animPosition.Duration = 2;
  
  // add the animation to the layer.
  /* the "position" key is used to overwrite the implicit animation created
  when the layer positino is set above*/
  layer.AddAnimation (animPosition, "position");
}
```

Este código cambia el `Position` de la capa mediante la creación de una ruta de acceso que se usa para definir una animación de fotogramas clave. Observe que la capa `Position` está establecida en el valor final de la `Position` de la animación. Sin esto, el nivel volverá repentinamente a su `Position` antes de la animación porque la animación solo cambia el valor de presentación y no el valor de modelo real. Al establecer el valor del modelo en el valor final de la animación, la capa permanece en su lugar al final de la animación.

Las siguientes capturas de pantallas muestran el nivel que contiene la imagen que se anima a través de la ruta de acceso especificada:

 ![En esta captura de pantalla se muestra el nivel que contiene la imagen que se anima a través de la ruta de acceso especificada](core-animation-images/12-explicit-animation.png)

## <a name="summary"></a>Resumen

En este artículo, analizamos las capacidades de animación proporcionadas a través de los marcos de *animación básicos* . Hemos examinado la animación básica, que muestra cómo impulsa animaciones en UIKit y cómo se puede usar directamente para el control de animación de nivel inferior.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación básica](/samples/xamarin/ios-samples/graphicsandanimation)
- [Gráficos básicos](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Tutorial de animación y gráficos](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animación básica](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)