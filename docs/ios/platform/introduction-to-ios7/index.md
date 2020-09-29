---
title: Introducción a iOS 7
description: En este artículo se tratan las nuevas API principales introducidas en iOS 7, incluidas las transiciones de los controladores de vistas, mejoras en las animaciones de UIView, UIKit Dynamics y el kit de texto. También se tratan algunos de los cambios en la interfaz de usuario y las nuevas capacidades de multitarea.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9f27fa550bb21f4261288e8a9a3d809a0ccc7de2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436504"
---
# <a name="introduction-to-ios-7"></a>Introducción a iOS 7

_En este artículo se tratan las nuevas API principales introducidas en iOS 7, incluidas las transiciones de los controladores de vistas, mejoras en las animaciones de UIView, UIKit Dynamics y el kit de texto. También se tratan algunos de los cambios en la interfaz de usuario y las nuevas capacidades de multitarea._

iOS 7 es una actualización principal de iOS. Incluye un diseño de interfaz de usuario completamente nuevo que pone el foco en el contenido en lugar de en el cromo de la aplicación. Junto con los cambios visuales, iOS 7 agrega una gran cantidad de API nuevas para crear interacciones y experiencias más enriquecidas. En este documento se analizan las nuevas tecnologías introducidas con iOS 7 y sirve como punto de partida para una mayor exploración.

## <a name="uiview-animation-enhancements"></a>Mejoras en la animación UIView

iOS 7 aumenta la compatibilidad con la animación en UIKit, lo que permite que las aplicaciones realicen tareas que antes requerían colocar directamente en el marco de animación básico. Por ejemplo, `UIView` ahora puede realizar animaciones de resorte, así como animaciones de fotogramas clave, que anteriormente `CAKeyframeAnimation` se aplicaron a `CALayer` .

### <a name="spring-animations"></a>Animaciones de Spring

 `UIView` ahora admite animar los cambios de propiedad con un efecto elástico. Para agregar esto, llame al `AnimateNotify` método o `AnimateNotifyAsync` , pasando los valores de la relación de amortiguación del muelle y la velocidad inicial del muelle, tal como se describe a continuación:

- `springWithDampingRatio` : Un valor entre 0 y 1, donde la oscilación aumenta para un valor menor.
- `initialSpringVelocity` : Velocidad inicial del muelle como un porcentaje de la distancia total de la animación por segundo.

El código siguiente produce un efecto elástico cuando cambia el centro de la vista de imagen:

```csharp
void AnimateWithSpring ()
{
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;

    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {

        imageView.Center = new CGPoint (imageView.Center.X, 400);

    }, null);
}
```

Este efecto de resorte hace que la vista de imagen parezca rebotar mientras completa la animación en una nueva ubicación de centro, como se muestra a continuación:

 ![Este efecto de resorte hace que la vista de imagen parezca rebotar mientras completa la animación en una nueva ubicación de centro.](images/spring-animation.png)

### <a name="keyframe-animations"></a>Animaciones de fotogramas clave

La `UIView` clase ahora incluye el `AnimateWithKeyframes` método para crear animaciones de fotogramas clave en un `UIView` . Este método es similar a otros `UIView` métodos de animación, salvo que `NSAction` se pasa un adicional como parámetro para incluir los fotogramas clave. Dentro de `NSAction` , se agregan los fotogramas clave mediante una llamada a `UIView.AddKeyframeWithRelativeStartTime` .

Por ejemplo, el fragmento de código siguiente crea una animación de fotogramas clave para animar el centro de una vista, así como para girar la vista:

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => {
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => {
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

Los dos primeros parámetros del `AddKeyframeWithRelativeStartTime` método especifican la hora de inicio y la duración del fotograma clave, respectivamente, como un porcentaje de la longitud de la animación total. En el ejemplo anterior se obtiene la animación de la vista de imagen en su nuevo centro en el primer segundo, seguido de la rotación de 90 grados en el siguiente segundo. Dado que la animación especifica `UIViewKeyframeAnimationOptions.Autoreverse` como una opción, ambos fotogramas clave se animan también en orden inverso. Por último, los valores finales se establecen en el estado inicial en el controlador de finalización.

Las capturas de pantallas siguientes muestran la animación combinada a través de los fotogramas clave:

 ![En estas capturas de pantallas se muestra la animación combinada a través de los fotogramas clave](images/keyframes.png)

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics es un nuevo conjunto de API de UIKit que permiten a las aplicaciones crear interacciones animadas basadas en la física. UIKit Dynamics encapsula un motor físico 2D para que esto sea posible.

La API es declarativa por naturaleza. Para declarar cómo se comportan las interacciones físicas, cree objetos, denominados *comportamientos* , para expresar conceptos físicos como la gravedad, las colisiones, los muelles, etc. A continuación, adjunte los comportamientos a otro objeto, denominado *animador dinámico*, que encapsula una vista. El animador dinámico se encarga de aplicar los comportamientos de física declarados a *los elementos dinámicos* : los elementos que implementan `IUIDynamicItem` , como un `UIView` .

Hay varios comportamientos primitivos distintos disponibles para desencadenar interacciones complejas, entre las que se incluyen:

- `UIAttachmentBehavior` : Adjunta dos elementos dinámicos de modo que se mueven juntos o adjunta un elemento dinámico a un punto de datos adjuntos.
- `UICollisionBehavior` : Permite que los elementos dinámicos participen en colisiones.
- `UIDynamicItemBehavior` : Especifica un conjunto general de propiedades que se van a aplicar a los elementos dinámicos, como elasticidad, densidad y fricción.
- `UIGravityBehavior` : Aplica la gravedad a un elemento dinámico, lo que hace que los elementos se aceleren en la dirección Gravitational.
- `UIPushBehavior` : Aplica Force a un elemento dinámico.
- `UISnapBehavior` : Permite que un elemento dinámico se ajuste a una posición con un efecto elástico.

Aunque hay muchos primitivos, el proceso general para agregar interacciones basadas en el física a una vista mediante UIKit Dynamics es coherente entre los distintos comportamientos:

1. Cree un animador dinámico.
1. Cree comportamientos.
1. Agregue comportamientos al animador dinámico.

### <a name="dynamics-example"></a>Ejemplo de Dynamics

Echemos un vistazo a un ejemplo que agrega gravedad y un límite de colisión a un `UIView` .

#### <a name="uigravitybehavior"></a>UIGravityBehavior

La adición de gravedad a una vista de imagen sigue los 3 pasos descritos anteriormente.

En este ejemplo, trabajaremos en el `ViewDidLoad` método. En primer lugar, agregue una `UIImageView` instancia de de la siguiente manera:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Esto crea una vista de imagen centrada en el borde superior de la pantalla. Para que la imagen "quede" con gravedad, cree una instancia de `UIDynamicAnimator` :

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`Toma una instancia de una referencia `UIView` o un `UICollectionViewLayout` , que contiene los elementos que se animarán según los comportamientos adjuntos.

A continuación, cree una `UIGravityBehavior` instancia de. Puede pasar uno o más objetos que implementan `IUIDynamicItem` , como `UIView` :

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

Al comportamiento se le pasa una matriz de `IUIDynamicItem` , que en este caso contiene la `UIImageView` instancia única que se anima.

Por último, agregue el comportamiento al animador dinámico:

```csharp
dynAnimator.AddBehavior (gravity);
```

Esto da como resultado que la imagen se anima hacia abajo con gravedad, como se muestra a continuación:

![Ubicación de la imagen inicial ](images/gravity2.png)
 ![ de la ubicación de la imagen final](images/gravity3.png)

Dado que no hay nada que restrinja los límites de la pantalla, la vista de imagen simplemente cae fuera de la parte inferior. Para restringir la vista de modo que la imagen esté en conflicto con los bordes de la pantalla, podemos agregar `UICollisionBehavior` . Trataremos esto en la sección siguiente.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Comenzaremos creando una `UICollisionBehavior` y agregándola al animador dinámico, al igual que hicimos para `UIGravityBehavior` .

Modifique el código para incluir `UICollisionBehavior` :

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior`Tiene una propiedad denominada `TranslatesReferenceBoundsIntoBoundry` . Si se establece en `true` , los límites de la vista de referencia se utilizarán como límite de colisión.

Ahora, cuando la imagen se anima hacia abajo con gravedad, rebota ligeramente hacia abajo en la parte inferior de la pantalla antes de la liquidación en el resto.

<!--, as shown below:

 ![Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there](images/bounce.png)-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Podemos controlar aún más el comportamiento de la vista de la imagen en caída con comportamientos adicionales. Por ejemplo, podríamos agregar un `UIDynamicItemBehavior` para aumentar la elasticidad, lo que hace que la vista de imagen rebote más cuando entra en conflicto con la parte inferior de la pantalla.

Al agregar un `UIDynamicItemBehavior` , se siguen los mismos pasos que con los demás comportamientos. En primer lugar, cree el comportamiento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

A continuación, agregue el comportamiento al animador dinámico:

 `dynAnimator.AddBehavior (dynBehavior);`

Con este comportamiento en contexto, la vista de imagen rebota más cuando entra en conflicto con el límite.

## <a name="general-user-interface-changes"></a>Cambios generales de la interfaz de usuario

Además de las nuevas API de UIKit, como UIKit Dynamics, transiciones de controlador y animaciones de UIView mejoradas que se han descrito anteriormente, iOS 7 introduce una variedad de cambios visuales en la interfaz de usuario y cambios relacionados con la API para varias vistas y controles. Para obtener más información, consulte la [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texto

El kit de texto es una nueva API que ofrece características eficaces de diseño y representación de texto. Se basa en el marco de texto básico de nivel inferior, pero es mucho más fácil de usar que el texto principal.

Para obtener más información, consulte nuestro [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitarea

iOS 7 cambia cuándo y cómo se realiza el trabajo en segundo plano. La finalización de la tarea en iOS 7 ya no mantiene las aplicaciones activas cuando las tareas se ejecutan en segundo plano y las aplicaciones se reactivarán para el procesamiento en segundo plano de forma no contigua. iOS 7 también agrega tres nuevas API para actualizar aplicaciones con contenido nuevo en segundo plano:

- Captura en segundo plano: permite que las aplicaciones actualicen el contenido en segundo plano a intervalos regulares.
- Notificaciones remotas: permite que las aplicaciones actualicen el contenido al recibir una notificación de envío. Las notificaciones pueden ser silenciosas o pueden mostrar un banner en la pantalla de bloqueo.
- Servicio de transferencia en segundo plano: permite cargar y descargar datos, como archivos grandes, sin un límite de tiempo fijo.

Para obtener más información sobre las nuevas funcionalidades de multitarea, vea las secciones de iOS de la guía de procesamiento en [segundo plano](~/ios/app-fundamentals/backgrounding/index.md)de Xamarin.

## <a name="summary"></a>Resumen

En este artículo se tratan varias novedades principales para iOS. En primer lugar, se muestra cómo agregar transiciones personalizadas a los controladores de vista. A continuación, muestra cómo usar las transiciones en las vistas de colección, tanto desde dentro de un controlador de navegación como interactivamente entre las vistas de colección. A continuación, se presentan varias mejoras realizadas en las animaciones de UIView, que muestran cómo las aplicaciones usan UIKit para las cosas que anteriormente requerían programar directamente con la animación básica. Por último, la nueva API de UIKit Dynamics, que incorpora un motor físico a UIKit, se introduce junto con la compatibilidad de texto enriquecido disponible en el marco de texto del kit.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](/samples/xamarin/ios-samples/introtoios7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)