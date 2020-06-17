---
title: "Adición de un reconocedor de gesto de deslizar rápidamente" description: "En este artículo se explica cómo reconocer un gesto de deslizar rápidamente que se produce en una vista."
ms.prod: xamarin ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B ms.technology: xamarin-forms author: davidbritch ms.author: dabritch ms.date: 08/14/2018 no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="adding-a-swipe-gesture-recognizer"></a>Agregar un reconocedor de gesto de deslizar rápidamente

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Un gesto de deslizar rápidamente se produce cuando un dedo se mueve a través de la pantalla en dirección horizontal o vertical y, a menudo, se usa para iniciar la navegación a través del contenido. Los ejemplos de código en este artículo están sacados del ejemplo [Swipe Gesture](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture) (Gesto de deslizar rápidamente)._

Para hacer que [`View`](xref:Xamarin.Forms.View) reconozca un gesto de deslizar rápidamente, cree una instancia de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer), establezca la propiedad [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) en un valor de enumeración de [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) (`Left`, `Right`, `Up` o `Down`), opcionalmente establezca la propiedad [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), controle el evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) y agregue el reconocedor de gestos nuevo a la colección [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) de la vista. En el ejemplo de código siguiente se muestra un elemento `SwipeGestureRecognizer` adjunto a [`BoxView`](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

El código equivalente en C# es el siguiente:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

La clase [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) también incluye una propiedad [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), que opcionalmente se puede establecer en un valor `uint` que representa la distancia mínima que debe deslizarse un dedo para que se reconozca el deslizamiento, en unidades independientes del dispositivo. El valor predeterminado de esta propiedad es 100, lo cual significa que cualquier deslizamiento por debajo de 100 unidades independientes del dispositivo se ignorará.

## <a name="recognizing-the-swipe-direction"></a>Reconocimiento de la dirección del deslizamiento

En los ejemplos anteriores, la propiedad [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) está establecida en un valor único de la enumeración de [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Sin embargo, también es posible establecer esta propiedad en varios valores desde la enumeración de `SwipeDirection`, por lo que el evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) se desencadena en respuesta a un deslizamiento en más de una dirección. Sin embargo, la restricción es que un único elemento [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) solo puede reconocer los deslizamientos que se produzcan en el mismo eje. Por lo tanto, se pueden reconocer los deslizamientos que se producen en el eje horizontal estableciendo la propiedad `Direction` en `Left` y `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

De forma similar, se pueden reconocer los deslizamientos que se producen en el eje horizontal estableciendo la propiedad [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) en `Up` y `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Como alternativa, puede crearse un [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) para cada dirección de deslizamiento para que reconozca los deslizamientos en cada dirección:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

El código equivalente en C# es el siguiente:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> En los ejemplos anteriores, el mismo controlador de eventos responde a la activación del evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped). Sin embargo, cada instancia de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) puede usar un controlador de eventos distinto, si es necesario.

## <a name="responding-to-the-swipe"></a>Respuesta al deslizamiento

En el ejemplo siguiente, se muestra un controlador de eventos para el evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped):

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

[`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) puede examinarse para determinar la dirección del deslizamiento, con lógica personalizada como respuesta al deslizamiento, según sea necesario. Se puede obtener la dirección del deslizamiento desde la propiedad [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) de los argumentos de evento, que se establecerán en uno de los valores de la enumeración de [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Además, los argumentos de evento también tienen una propiedad [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) que se establecerá en el valor de la propiedad [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter), si se ha definido.

## <a name="using-commands"></a>Uso de comandos

La clase [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) también incluye las propiedades [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) y [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Estas propiedades se utilizan normalmente en aplicaciones que usan el patrón Model-View-ViewModel (MVVM). La propiedad `Command` define el elemento `ICommand` que se invocará cuando se reconoce un gesto de deslizar rápidamente, con la propiedad `CommandParameter` que define un objeto que se pasará al elemento `ICommand.`. El ejemplo de código siguiente muestra cómo enlazar la propiedad `Command` a un elemento `ICommand` definido en el modelo de vista cuya instancia se ha establecido como la página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext):

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

El código XAML equivalente es:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` es una propiedad de tipo `ICommand` definida en la instancia de modelo de vista que se establece como la página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Cuando se reconoce un gesto de deslizar rápidamente, se ejecuta el método `Execute` del objeto `SwipeCommand`. El argumento para el método `Execute` es el valor de la propiedad [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Para obtener más información sobre los comandos, consulte [The Command Interface](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) (La interfaz de comandos).

## <a name="creating-a-swipe-container"></a>Creación de un contenedor de deslizamiento

La clase `SwipeContainer`, que se muestra en el siguiente ejemplo de código, es una clase de reconocimiento de deslizamiento que se ha ajustado alrededor de un elemento [`View`](xref:Xamarin.Forms.View) para realizar el reconocimiento de gestos de deslizar rápidamente:

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

La clase `SwipeContainer` crea objetos [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) para las cuatro direcciones de deslizamiento y adjunta `Swipe` controladores de eventos. Estos controladores de eventos invocan el evento `Swipe` definido por `SwipeContainer`.

En el ejemplo de código XAML siguiente se muestra la clase `SwipeContainer` que realiza el ajuste de [`BoxView`](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

En el ejemplo de código siguiente se muestra cómo `SwipeContainer` realiza el ajuste de [`BoxView`](xref:Xamarin.Forms.BoxView) en una página de C#:

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

Cuando [`BoxView`](xref:Xamarin.Forms.BoxView) recibe un gesto de deslizar rápidamente, el evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) en [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) se activa. Esto se controla mediante la clase `SwipeContainer`, que activa su propio evento `Swipe`. Este evento `Swipe` se controla en la página. Posteriormente, [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) puede examinarse para determinar la dirección del deslizamiento, con lógica personalizada como respuesta al deslizamiento, según sea necesario.

## <a name="related-links"></a>Vínculos relacionados

- [Gesto de deslizar rápidamente (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
