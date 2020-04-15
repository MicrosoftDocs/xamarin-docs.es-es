---
title: Invocación de eventos desde efectos
description: Un efecto puede definir e invocar un evento, señalando cambios en la vista nativa subyacente. En este artículo se muestra cómo implementar seguimiento multitáctil de bajo nivel y cómo se generan eventos que indican actividad táctil.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: cf5a97bca7c827101db951a440863839539c7e48
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725265"
---
# <a name="invoking-events-from-effects"></a>Invocación de eventos desde efectos

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)

_Un efecto puede definir e invocar un evento, señalando cambios en la vista nativa subyacente. En este artículo se muestra cómo implementar seguimiento multitáctil de bajo nivel y cómo se generan eventos que indican actividad táctil._

El efecto que se describe en este artículo proporciona acceso a eventos de función táctil de bajo nivel. Estos eventos de bajo nivel no están disponibles a través de las clases `GestureRecognizer` existentes, pero son vitales para algunos tipos de aplicaciones. Por ejemplo, una aplicación de dibujo táctil necesita realizar un seguimiento de los dedos individuales cuando se mueven en la pantalla. Un teclado musical debe detectar cuándo se pulsan y sueltan teclas individuales, así como un deslizamiento de dedos de una clave a otra en un glissando.

Un efecto es ideal para el seguimiento multitáctil, ya que puede asociarse a cualquier elemento de Xamarin.Forms.

## <a name="platform-touch-events"></a>Eventos táctiles de plataforma

iOS, Android y la Plataforma universal de Windows incluyen una API de bajo nivel que permite a las aplicaciones detectar la actividad táctil. Todas estas plataformas distinguen entre tres tipos básicos de eventos táctiles:

- *Presionado*, cuando un dedo toca la pantalla
- *Movido*, cuando se mueve un dedo tocando la pantalla
- *Soltado*, cuando se separa el dedo de la pantalla

En un entorno multitáctil, varios dedos pueden tocar la pantalla al mismo tiempo. Las diferentes plataformas incluyen un número de identificación (ID) que las aplicaciones pueden usar para distinguir entre varios dedos.

En iOS, la clase `UIView` define tres métodos reemplazables, `TouchesBegan`, `TouchesMoved` y `TouchesEnded` correspondientes a estos tres eventos básicos. En el artículo [Multi-Touch Finger Tracking](~/ios/app-fundamentals/touch/touch-tracking.md) (Seguimiento multitáctil) se describe cómo usar estos métodos. Sin embargo, un programa de iOS no necesita invalidar una clase que derive de `UIView` para usar estos métodos. `UIGestureRecognizer` de iOS también define estos mismos tres métodos y se puede adjuntar a una instancia de una clase que deriva de `UIGestureRecognizer` a cualquier objeto `UIView`.

En Android, la clase `View` define un método reemplazable denominado `OnTouchEvent` para procesar toda la actividad táctil. El tipo de la actividad táctil se define por miembros de la enumeración `Down`, `PointerDown`, `Move`, `Up` y `PointerUp` tal como se describe en el artículo [Multi-Touch Finger Tracking](~/android/app-fundamentals/touch/touch-tracking.md) (Seguimiento multitáctil). `View` de Android también define un evento denominado `Touch` que permite que un controlador de eventos se adjunte a cualquier objeto `View`.

En la Plataforma universal de Windows (UWP), la clase `UIElement` define eventos denominados `PointerPressed`, `PointerMoved` y `PointerReleased`. Estos se describen en el artículo [Handle pointer input](/windows/uwp/input-and-devices/handle-pointer-input/) (Controlar la entrada del puntero) de MSDN y en la documentación de API para la clase [`UIElement`](/uwp/api/windows.ui.xaml.uielement/).

La API `Pointer` en la Plataforma universal de Windows está diseñada para unificar la entrada de mouse, táctil y manuscrita. Por ese motivo, el evento `PointerMoved` se invoca cuando el mouse se mueve a través de un elemento, incluso cuando no se ha presionado ningún botón del mouse. El objeto `PointerRoutedEventArgs` que acompaña a estos eventos tiene una propiedad denominada `Pointer`, que tiene una propiedad denominada `IsInContact`, que indica si se presiona un botón del mouse o un dedo está en contacto con la pantalla.

Además, la UWP define dos eventos más denominados `PointerEntered` y `PointerExited`. Estos indican si un dedo o el mouse se mueven de un elemento a otro. Por ejemplo, imagine dos elementos adyacentes denominados A y B. Ambos elementos tienen controladores instalados para los eventos de puntero. Cuando se presiona un dedo en A, el evento `PointerPressed` se invoca. Cuando se mueve el dedo, A invoca eventos `PointerMoved`. Si el dedo se mueve de A a B, A invoca un evento `PointerExited` y B invoca un evento `PointerEntered`. Si después se suelta el dedo, B invoca un evento `PointerReleased`.

Las plataformas iOS y Android son diferentes de UWP: la vista que obtiene primero la llamada a `TouchesBegan` o `OnTouchEvent` cuando un dedo toca la vista continúa obteniendo toda la actividad de interacción, incluso si se mueve el dedo a distintas vistas. UWP puede comportarse de forma similar si la aplicación captura el puntero: en el controlador de eventos `PointerEntered`, el elemento llama a `CapturePointer` y después obtiene toda la actividad táctil de ese dedo.

El enfoque de UWP resulta muy útil para algunos tipos de aplicaciones, por ejemplo, un teclado musical. Cada clave puede controlar los eventos táctiles para esa clave y detectar cuando un dedo se deslizó de una tecla a otra mediante los eventos `PointerEntered` y `PointerExited`.

Por ese motivo, el efecto de seguimiento táctil que se describe en este artículo implementa el enfoque de UWP.

## <a name="the-touch-tracking-effect-api"></a>La API de efecto de seguimiento táctil

El ejemplo de [**Touch Tracking Effect Demos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) (Demostraciones de efecto de seguimiento táctil) contiene las clases (y una enumeración) que implementan el seguimiento táctil de bajo nivel. Estos tipos pertenecen al espacio de nombres `TouchTracking` y comienzan con la palabra `Touch`. El proyecto de biblioteca de .NET Standard **TouchTrackingEffectDemos** incluye la enumeración `TouchActionType` para el tipo de eventos táctiles:

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

Todas las plataformas incluyen también un evento que indica que se ha cancelado el evento táctil.

La clase `TouchEffect` de la biblioteca .NET Standard deriva de `RoutingEffect` y define un evento denominado `TouchAction` y un método denominado `OnTouchAction` que invoca al evento `TouchAction`:

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

Observe también la propiedad `Capture`. Para capturar eventos táctiles, una aplicación debe establecer esta propiedad en `true` antes de un evento `Pressed`. En caso contrario, los eventos táctiles se comportan como los de la Plataforma universal de Windows.

La clase `TouchActionEventArgs` en la biblioteca de .NET Standard contiene toda la información que acompaña a cada evento:

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

Una aplicación puede utilizar la propiedad `Id` para el seguimiento de dedos individuales. Observe la propiedad `IsInContact`. Esta propiedad es siempre `true` para eventos `Pressed` y `false` para eventos `Released`. También es siempre `true` para eventos `Moved` en iOS y Android. La propiedad `IsInContact` podría ser `false` para `Moved` eventos en la Plataforma universal de Windows cuando se ejecuta el programa en el escritorio y se mueve el puntero del mouse sin un botón presionado.

Puede usar la clase `TouchEffect` en sus propias aplicaciones mediante la inclusión del archivo en el proyecto de biblioteca de .NET Standard de la solución y mediante la adición de una instancia a la colección de `Effects` de cualquier elemento de Xamarin.Forms. Adjunte un controlador al evento `TouchAction` para obtener los eventos táctiles.

Para usar `TouchEffect` en su propia aplicación, también necesitará las implementaciones de plataforma incluidas en la solución **TouchTrackingEffectDemos**.

## <a name="the-touch-tracking-effect-implementations"></a>Las implementaciones de efecto de seguimiento táctil

Las implementaciones de iOS, Android y UWP de `TouchEffect` se describen aquí comenzando con la implementación más sencilla (UWP) y terminando con la implementación de iOS porque es estructuralmente más compleja que las demás.

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `TouchEffect` es la más sencilla. Como de costumbre, la clase se deriva de `PlatformEffect` e incluye dos atributos de ensamblado:

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

La invalidación `OnAttached` guarda información, como los campos, y adjunta los controladores a todos los eventos de puntero:

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

El controlador `OnPointerPressed` invoca el evento de efecto llamando al campo `onTouchAction` en el método `CommonHandler`:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` también comprueba el valor de la propiedad `Capture` en la clase efecto en la biblioteca de .NET Standard y llama a `CapturePointer` si es `true`.

 Los otros controladores de eventos de UWP son incluso más sencillos:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>La implementación de Android

Las implementaciones de Android y iOS son necesariamente más complejas porque deben implementar los eventos `Exited` y `Entered` cuando un dedo se mueve de un elemento a otro. Ambas implementaciones tienen una estructura similar.

La clase `TouchEffect` de Android instala un controlador para el evento `Touch`:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

La clase también define dos diccionarios estáticos:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

`viewDictionary` obtiene una nueva entrada cada vez que se llama a la invalidación `OnAttached`:

```csharp
viewDictionary.Add(view, this);
```

La entrada se quita del diccionario en `OnDetached`. Todas las instancias de `TouchEffect` están asociadas a una vista concreta a la que está conectada el efecto. El diccionario estático permite cualquier instancia de `TouchEffect` para enumerar todas las demás vistas y sus correspondientes instancias de `TouchEffect`. Esto es necesario para permitir la transferencia de los eventos de una vista a otra.

Android asigna un código de identificador a los eventos táctiles que permite que una aplicación realice un seguimiento de los dedos individuales. El `idToEffectDictionary` asocia este código de identificador con una instancia de `TouchEffect`. Se agrega un elemento a este diccionario cuando se llama al controlador `Touch` para una pulsación de dedo:

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

El elemento se quita de `idToEffectDictionary` cuando se separa el dedo de la pantalla. El método `FireEvent` simplemente acumula toda la información necesaria para llamar al método `OnTouchAction`:

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

Se procesan todos los demás tipos de interacción de dos maneras diferentes: si la propiedad `Capture` es `true`, el evento táctil es una traducción bastante simple para la información de `TouchEffect`. Se complica más cuando `Capture` es `false`, porque los eventos táctiles quizás tengan que moverse de una vista a otra. Esta es la responsabilidad del método `CheckForBoundaryHop`, al que se llama durante los eventos de movimiento. Este método usa ambos diccionarios estáticos. Enumera a través de `viewDictionary` para determinar la vista que actualmente esté tocando el dedo y usa `idToEffectDictionary` para almacenar la instancia de `TouchEffect` actual (y por lo tanto, la vista actual) asociada con un identificador determinado:

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

Si ha habido un cambio en el `idToEffectDictionary`, el método potencialmente llama a `FireEvent` para `Exited` y `Entered` para transferir de una vista a otra. Con todo, es posible que el dedo se moviese a un área ocupada por una vista sin un `TouchEffect` adjunto o desde esa área a una vista con el efecto adjunto.

Tenga en cuenta el bloqueo `try` y `catch` cuando se accede a la vista. En una página a la que se navega que después navega a la página principal, no se llama al método `OnDetached` y los elementos permanecen en el `viewDictionary` pero Android los considera eliminados.

### <a name="the-ios-implementation"></a>La implementación de iOS

La implementación de iOS es similar a la implementación de Android, salvo que la clase `TouchEffect` de iOS debe crear una instancia de un derivado de `UIGestureRecognizer`. Se trata de una clase en el proyecto de iOS denominado `TouchRecognizer`. Esta clase mantiene dos diccionarios estáticos que almacenan instancias de `TouchRecognizer`:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Gran parte de la estructura de esta clase `TouchRecognizer` es similar a la de la clase `TouchEffect` de Android.

> [!IMPORTANT]
> Muchas de las vistas de `UIKit` no tienen la funcionalidad táctil habilitada de forma predeterminada. La funcionalidad táctil se puede habilitar agregando `view.UserInteractionEnabled = true;` a la invalidación `OnAttached` en la clase `TouchEffect` en el proyecto de iOS. Esto debe ocurrir después obtener `UIView`, que se corresponde con el elemento al que está asociado el efecto.

## <a name="putting-the-touch-effect-to-work"></a>Poner el efecto táctil en funcionamiento

El programa [**TouchTrackingEffectDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) contiene cinco páginas que prueban el efecto de seguimiento táctil para tareas comunes.

La página **Arrastre de BoxView** le permite agregar elementos `BoxView` a un `AbsoluteLayout` y después arrastrarlos en la pantalla. El [archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml) crea instancias de dos vistas de `Button` para agregar elementos `BoxView` a la `AbsoluteLayout` y borrar el `AbsoluteLayout`.

El método en el [archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml.cs) que agrega un nuevo `BoxView` a `AbsoluteLayout` también agrega un objeto `TouchEffect` para `BoxView` y adjunta un controlador de eventos para el efecto:

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

El controlador de eventos `TouchAction` procesa todos los eventos táctiles para todos los elementos de `BoxView`, pero debe usarse con cuidado: no puede permitir dos dedos en una sola `BoxView` porque el programa solo implementa el arrastre y los dos dedos podrían interferir entre sí. Por este motivo, la página define una clase incrustada para cada dedo del que está realizando el seguimiento:

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

El `dragDictionary` contiene una entrada para cada `BoxView` que se arrastra actualmente.

La acción táctil `Pressed` agrega un elemento a este diccionario y la acción `Released` lo quita. La lógica `Pressed` debe comprobar si ya hay un elemento en el diccionario para esa `BoxView`. Si es así, `BoxView` ya se está arrastrando y el nuevo evento es un segundo dedo en esa misma `BoxView`. Para las acciones `Moved` y `Released`, el controlador de eventos debe comprobar si el diccionario tiene una entrada para esa `BoxView` y que la propiedad `Id` táctil para la `BoxView` arrastrada coincide con el que aparece en la entrada del diccionario:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

La lógica `Pressed` establece la propiedad `Capture` del objeto `TouchEffect` en `true`. Esto tiene el efecto de entregar todos los eventos subsiguientes para ese dedo en el mismo controlador de eventos.

La lógica `Moved` mueve la `BoxView` modificando la propiedad adjunta `LayoutBounds`. La propiedad `Location` de los argumentos de evento siempre es relativa a la `BoxView` que se está arrastrando y si el `BoxView` se está arrastrando a una velocidad constante, las propiedades `Location` de los eventos consecutivos serán aproximadamente las mismas. Por ejemplo, si un dedo presiona el `BoxView` en su centro, la acción `Pressed` almacena una propiedad `PressPoint` de (50, 50), que sigue siendo la misma para los eventos posteriores. Si la `BoxView` se arrastra en diagonal a una velocidad constante, las propiedades `Location` subsiguientes durante la acción `Moved` podrían ser valores de (55, 55), en cuyo caso la lógica `Moved` agrega 5 a la posición horizontal y vertical de la `BoxView`. Esto mueve la `BoxView` de forma que su centro está de nuevo directamente bajo el dedo.

Puede mover varios elementos `BoxView` al mismo tiempo usando diferentes dedos.

[![](touch-tracking-images/boxviewdragging-small.png "Triple screenshot of the BoxView Dragging page")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple screenshot of the BoxView Dragging page")

### <a name="subclassing-the-view"></a>Creación de subclases de la vista

A menudo, resulta más fácil para un elemento de Xamarin.Forms controlar sus propios eventos táctiles. La página **Arrastre de BoxView arrastrable** funciona igual que la página **Arrastre de BoxView**, pero los elementos que el usuario arrastra son instancias de una clase [`DraggableBoxView`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/DraggableBoxView.cs) que se deriva de `BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

El constructor crea y adjunta el `TouchEffect` y establece la propiedad `Capture` cuando se crea una instancia de ese objeto por primera vez. No se necesita ningún diccionario porque la propia clase almacena valores `isBeingDragged`, `pressPoint` y `touchId` asociados con cada dedo. El control `Moved` modifica las propiedades `TranslationX` y `TranslationY`, por lo que la lógica funcionará incluso si el elemento primario de la `DraggableBoxView` no es un `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Integración con SkiaSharp

Las siguientes dos demostraciones requieren gráficos y usan SkiaSharp para este propósito. Es posible que desee obtener información acerca de [Using SkiaSharp in Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) (Usar SkiaSharp en Xamarin.Forms) antes de estudiar estos ejemplos. Los dos primeros artículos ("Conceptos básicos de dibujo de SkiaSharp" y "Rutas y líneas de acceso de SkiaSharp") incluyen todo lo que necesitará aquí.

La página **Ellipse Drawing** (Dibujo de elipse) le permite dibujar una elipse deslizando el dedo en la pantalla. Dependiendo de cómo mueva el dedo, puede dibujar la elipse desde la esquina superior izquierda a la esquina inferior derecha, o desde cualquier otra esquina hasta la esquina opuesta. La elipse se dibuja con un color aleatorio y una opacidad.

[![](touch-tracking-images/ellipsedrawing-small.png "Triple screenshot of the Ellipse Drawing page")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple screenshot of the Ellipse Drawing page")

Si después toca uno de los puntos suspensivos, puede arrastrarla a otra ubicación. Esto requiere una técnica conocida como "prueba de posicionamiento," lo que implica buscar el objeto gráfico en un momento determinado. Los puntos suspensivos de SkiaSharp no son elementos de Xamarin.Forms, por lo que no pueden realizar su propio procesamiento de `TouchEffect`. El `TouchEffect` debe aplicarse a todo el objeto `SKCanvasView`.

El archivo [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml) crea una instancia de `SKCanvasView` en un `Grid` de una sola celda. El objeto `TouchEffect` se asocia a ese `Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

En la Plataforma universal de Windows y en Android el `TouchEffect` se puede conectar directamente a la `SKCanvasView`, pero en iOS no es posible. Tenga en cuenta que la propiedad `Capture` está establecida en `true`.

Cada elipse que representa SkiaSharp se representa mediante un objeto de tipo `EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

Las propiedades `StartPoint` y `EndPoint` se utilizan cuando el programa está procesando la entrada táctil; la propiedad `Rectangle` se utiliza para dibujar la elipse. La propiedad `LastFingerLocation` entra en juego cuando se arrastra la elipse y el método `IsInEllipse` ayuda en la prueba de posicionamiento. El método devuelve `true` si el punto está dentro de la elipse.

El [archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml.cs) mantiene tres colecciones:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

El diccionario `draggingFigure` contiene un subconjunto de la colección de `completedFigures`. El controlador de eventos `PaintSurface` de SkiaSharp representa simplemente los objetos en las colecciones `completedFigures` y `inProgressFigures`:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

La parte más complicada del procesamiento táctil es el control `Pressed`. Aquí es donde se realiza la prueba de posicionamiento, pero si el código detecta una elipse bajo el dedo del usuario, dicha elipse solo se puede arrastrar si actualmente no la está arrastrando otro dedo. Si no hay ninguna elipse bajo el dedo del usuario, el código comienza el proceso de dibujar una elipse nueva:

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

Otro ejemplo de SkiaSharp es la página **Finger Paint** (Dibujo con los dedos). Puede seleccionar un color de trazo y el ancho del trazo desde dos vistas `Picker` y después dibujar con uno o más dedos:

[![](touch-tracking-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Este ejemplo también requiere una clase independiente para representar cada línea dibujada en la pantalla:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

Un objeto `SKPath` se usa para representar cada línea. El archivo [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/FingerPaintPage.xaml.cs) mantiene dos colecciones de estos objetos, una para aquellas polilíneas que se están dibujando actualmente y otra para las polilíneas completadas:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

El procesamiento de `Pressed` crea una nueva `FingerPaintPolyline`, llama a `MoveTo` en el objeto de ruta de acceso para almacenar el punto inicial y agrega ese objeto al diccionario de `inProgressPolylines`. El procesamiento de `Moved` llama a `LineTo` en el objeto de ruta con la nueva posición del dedo y el procesamiento de `Released` transfiere la polilínea completada desde `inProgressPolylines` a `completedPolylines`. Una vez más, el código de dibujo de SkiaSharp real es relativamente sencillo:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>Seguimiento táctil de vista a vista

Todos los ejemplos anteriores han establecido la propiedad `Capture` del `TouchEffect` en `true`, ya sea cuando el `TouchEffect` se creó o cuando se produjo el evento `Pressed`. Esto garantiza que el mismo elemento recibe todos los eventos asociados con el dedo que presionó la vista primero. El ejemplo final *no* establece `Capture` en `true`. Esto provoca un comportamiento diferente cuando se mueve un dedo en contacto con la pantalla de un elemento a otro. El elemento desde el que se mueve el dedo recibe un evento con una propiedad `Type` establecida en `TouchActionType.Exited` y el segundo elemento recibe un evento con una configuración `Type` de `TouchActionType.Entered`.

Este tipo de procesamiento táctil es muy útil para un teclado de música. Una tecla debe ser capaz de detectar cuándo se pulsa, pero también cuándo un dedo pasa de una tecla a otra.

La página **Silent Keyboard** teclado silencioso define clases [`WhiteKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/WhiteKey.cs) y [`BlackKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BlackKey.cs) pequeñas que derivan de [`Key`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/Key.cs), que se deriva de `BoxView`.

La clase `Key` está lista para usarse en un programa de música real. Define propiedades públicas denominadas `IsPressed` y `KeyNumber`, que están pensadas para establecerse en el código de teclas que establece el estándar MIDI. La clase `Key` también define un evento denominado `StatusChanged`, que se invoca cuando la propiedad `IsPressed` cambia.

Se permiten varios dedos en cada tecla. Por este motivo, la clase `Key` mantiene una `List` de los números de Id. táctil de todos los dedos que tocan actualmente esa tecla:

```csharp
List<long> ids = new List<long>();
```

El controlador de eventos `TouchAction` agrega un identificador para la lista `ids` para un tipo de evento `Pressed` y un tipo `Entered`, pero solo cuando la propiedad `IsInContact` es `true` para el evento `Entered`. Se quita el identificador de la `List` para un evento `Released` o `Exited`:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

Los métodos `AddToList` y `RemoveFromList` comprueban si la `List` ha cambiado entre vacía y no vacía y si es así, invocan el evento `StatusChanged`.

Los distintos elementos `WhiteKey` y `BlackKey` se organizan en el [archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/SilentKeyboardPage.xaml) de la página, que tiene mejor aspecto cuando se mantiene el teléfono en un modo horizontal:

[![](touch-tracking-images/silentkeyboard-small.png "Triple screenshot of the Silent Keyboard page")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple screenshot of the Silent Keyboard page")

Si pasa los dedos por las teclas, podrá ver por los pequeños cambios en el color que los eventos táctiles se transfieren de una tecla a otra.

## <a name="summary"></a>Resumen

En este artículo se mostró cómo invocar eventos en un efecto, y cómo escribir y usar un efecto que implementa el procesamiento multitáctil de bajo nivel.

## <a name="related-links"></a>Vínculos relacionados

- [Multi-Touch Finger Tracking in iOS](~/ios/app-fundamentals/touch/touch-tracking.md) (Seguimiento de dedos multitáctil en iOS)
- [Multi-Touch Finger Tracking in Android](~/android/app-fundamentals/touch/touch-tracking.md) (Seguimiento de dedos multitáctil en Android)
- [Touch Tracking Effect (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) (Efecto de seguimiento táctil [ejemplo])
