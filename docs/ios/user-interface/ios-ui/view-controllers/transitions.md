---
title: Ver transiciones del controlador en Xamarin. iOS
description: En este documento se describe cómo personalizar transiciones animadas entre controladores de vista en aplicaciones de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: f0886ac9d47e7ab08a6f74365bcc25163b803e11
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002404"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Ver transiciones del controlador en Xamarin. iOS

UIKit agrega compatibilidad para personalizar la transición animada que se produce cuando se presentan controladores de vista. Esta compatibilidad se incluye con los Controladores integrados, así como con los controladores personalizados que heredan directamente de `UIViewController`. Además, `UICollectionViewController` aprovecha la personalización de la transición del controlador para aprovechar las transiciones animadas en los diseños de la vista de colección.

## <a name="custom-transitions"></a>Transiciones personalizadas

La transición animada entre controladores de vista en iOS 7 es totalmente personalizable. `UIViewController` ahora incluye una propiedad `TransitioningDelegate` que proporciona una clase de animación personalizada al sistema cuando se produce una transición.

Para usar una transición personalizada con `PresentViewController`:

1. Establezca el `ModalPresentationStyle` en `UIModalPresentationStyle.Custom` en el controlador que se va a presentar.
2. Implemente `UIViewControllerTransitioningDelegate` para crear una clase de animación, que es una instancia de `UIViewControllerAnimatedTransitioning`.
3. Establezca la propiedad `TransitioningDelegate` en una instancia de `UIViewControllerTransitioningDelegate`, también en el controlador que se va a presentar.
4. Presente el controlador de vista.

Por ejemplo, el código siguiente presenta un controlador de vista de tipo `ControllerTwo`-a `UIViewController` subclase:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Ejecutar la aplicación y pulsar el botón hace que la animación predeterminada de la segunda vista del controlador se anime en la parte inferior, como se muestra a continuación:

 ![](transitions-images/no-custom-transition.png "Running the app and tapping the button causes the default animation of the second controllers view to animate in from the bottom")

Sin embargo, si se establece el `ModalPresentationStyle` y el `TransitioningDelegate`, se produce una animación personalizada para la transición:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

El `TransitioningDelegate` es responsable de crear una instancia de la subclase `UIViewControllerAnimatedTransitioning`-denominada `CustomAnimator` en el ejemplo siguiente:

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Cuando se produce la transición, el sistema crea una instancia de `IUIViewControllerContextTransitioning`, que se pasa a los métodos del animador. `IUIViewControllerContextTransitioning` contiene el `ContainerView` en el que se produce la animación, así como el controlador de vista que inicia la transición y el controlador de vista al que se está realizando la transición.

La clase `UIViewControllerAnimatedTransitioning` controla la animación real. Se deben implementar dos métodos:

1. `TransitionDuration`: devuelve la duración de la animación en segundos.
1. `AnimateTransition`: realiza la animación real.

Por ejemplo, la clase siguiente implementa `UIViewControllerAnimatedTransitioning` para animar el marco de la vista del controlador:

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
    {
        var inView = transitionContext.ContainerView;
        var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
        var toView = toVC.View;

        inView.AddSubview (toView);

        var frame = toView.Frame;
        toView.Frame = CGRect.Empty;

        UIView.Animate (TransitionDuration (transitionContext), () => {
            toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
        }, () => {
            transitionContext.CompleteTransition (true);
        });
    }
}
```

Ahora, cuando se puntee en el botón, se usa la animación implementada en la clase `UIViewControllerAnimatedTransitioning`:

 ![](transitions-images/custom-transition.png "An example of the zoom in effect running")

## <a name="collection-view-transitions"></a>Transiciones de la vista de colección

Las vistas de colección tienen compatibilidad integrada para crear transiciones animadas:

- **Controladores de navegación** : la transición animada entre dos instancias de `UICollectionViewController` se puede controlar opcionalmente de forma automática cuando un `UINavigationController` las administra.
- **Diseño de transición** : una nueva clase de `UICollectionViewTransitionLayout` permite la transición interactiva entre diseños.

### <a name="navigation-controller-transitions"></a>Transiciones del controlador de navegación

Cuando se usa dentro de un controlador de navegación, una `UICollectionViewController` incluye compatibilidad con transiciones animadas entre controladores. Esta compatibilidad está integrada y requiere solo unos pocos pasos sencillos para implementar:

1. Establezca `UseLayoutToLayoutNavigationTransitions` en `false` en un `UICollectionViewController`.
1. Agregue una instancia de la `UICollectionViewController` a la raíz de la pila del controlador de navegación.
1. Cree una segunda `UICollectionViewController` y establezca su propiedad `UseLayoutToLayoutNavigtionTransitions` en `true`.
1. Inserte el segundo `UICollectionViewController` en la pila del controlador de navegación.

En el código siguiente se agrega una subclase `UICollectionViewController` denominada `ImagesCollectionViewController` a la raíz de la pila de un controlador de navegación, con la propiedad `UseLayoutToLayoutNavigationTransitions` establecida en `false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false
        };

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();

    return true;
}
```

Cuando se selecciona un elemento, se crea una segunda instancia de la `ImagesController`, pero esta vez solo se usa una clase de diseño diferente. En este controlador, `UseLayoutToLayoutNavigtionTransitions` se establece en `true`, como se muestra a continuación:

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
    circleLayout = new CircleLayout (Monkeys.Instance.Count){
        ItemSize = new CGSize (100, 100)
    };

    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true
        };

    NavigationController.PushViewController (controller2, true);
}
```

La propiedad `UseLayoutToLayoutNavigationTransitions` se debe establecer antes de agregar el controlador a la pila de navegación. Con esta propiedad establecida, la transición horizontal normal se sustituye por una transición animada entre los diseños de los dos controladores, como se muestra a continuación:

![](transitions-images/nav2.png "An animated transition between the layouts of the two controllers")

### <a name="transition-layout"></a>Diseño de transición

Además de la compatibilidad con la transición de diseño dentro de los controladores de navegación, ahora está disponible un nuevo diseño denominado `UICollectionViewTransitionLayout`. Esta clase de diseño permite el control interactivo durante el proceso de transición del diseño, permitiendo que el `TransitionProgress` se establezca desde el código. `UICollectionViewTransitionLayout` es diferente de-y no un reemplazo para el método `SetCollectionViewLayout` de iOS 6 que hizo que se produjera una transición de diseño animado. Ese método no proporcionó compatibilidad integrada para controlar el progreso de la transición animada.

 `UICollectionViewTransitionLayout` permite, por ejemplo, que se configure un reconocedor de gestos para controlar la transición entre los diseños en respuesta a la interacción del usuario, mediante la administración del diseño original, así como el diseño previsto para realizar la transición a.

Los pasos para implementar una transición interactiva dentro de un reconocedor de gestos mediante `UICollectionViewTransitionLayout` son los siguientes:

1. Cree un reconocedor de gestos.
1. Llame al método `StartInteractiveTransition` del `UICollectionView`, pasándole el diseño de destino y un controlador de finalización.
1. Establezca la propiedad `TransitionProgress` de la instancia de `UICollectionViewTransitionLayout` devuelta desde el método `StartInteractiveTransition`.
1. Invalidar el diseño.
1. Llame al método `FinishInteractiveTransition` del `UICollectionView` para completar la transición o el método `CancelInteractiveTransition` para cancelarla.  `FinishInteractiveTransition` hace que la animación complete su transición al diseño de destino, mientras que `CancelInteractiveTransition` hace que la animación vuelva al diseño original.
1. Controla la finalización de la transición en el controlador de finalización del método `StartInteractiveTransition`.
1. Agregue el reconocedor de gestos a la vista de colección.

El código siguiente implementa una transición de diseño interactivo dentro de un reconocedor de gestos de pinch:

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

A medida que el usuario reduce la vista de colección, el `TransitionProgress` se establece en relación con la escala del contacto. En esta implementación, si el usuario finaliza el contacto antes de que se complete la transición del 50%, se cancela la transición. De lo contrario, la transición ha finalizado.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
