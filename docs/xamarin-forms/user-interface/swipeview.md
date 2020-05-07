---
title: SwipeView de Xamarin. Forms
description: Xamarin. Forms SwipeView es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/26/2020
ms.openlocfilehash: 992e4dd1a2b2a1d1a4f0b76dadf4704241486415
ms.sourcegitcommit: 520ea9d52266f745d2c09642bac21f64a56f8c31
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2020
ms.locfileid: "82859111"
---
# <a name="xamarinforms-swipeview"></a>SwipeView de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView` Es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos en una CollectionView, en iOS y Android](swipeview-images/swipeview-collectionview.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

`SwipeView`está disponible en Xamarin. Forms 4,4. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la `AppDelegate` clase en iOS, a la `MainActivity` clase en Android o a la `App` clase en UWP, antes de llamar `Forms.Init`a:

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` define las siguientes propiedades:

- `LeftItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo por el control hacia la izquierda.
- `RightItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo por el control hacia la derecha.
- `TopItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando el control se desliza hacia arriba hacia abajo.
- `BottomItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo hacia arriba en el control.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Además, `SwipeView` hereda la [`Content`](xref:Xamarin.Forms.ContentView.Content) propiedad de la [`ContentView`](xref:Xamarin.Forms.ContentView) clase. La `Content` propiedad es la propiedad de contenido de `SwipeView` la clase y, por tanto, no es necesario establecer explícitamente.

La `SwipeView` clase también define cuatro eventos:

- `SwipeStarted`se desencadena cuando se inicia un deslizamiento. El `SwipeStartedEventArgs` objeto que acompaña a este evento tiene una `SwipeDirection` propiedad, de tipo `SwipeDirection`.
- `SwipeChanging`se desencadena cuando se mueve el dedo. El `SwipeChangingEventArgs` objeto que acompaña a este evento tiene una `SwipeDirection` propiedad, de tipo `SwipeDirection`y una `Offset` propiedad de tipo `double`.
- `SwipeEnded`se desencadena cuando finaliza un deslizamiento. El `SwipeEndedEventArgs` objeto que acompaña a este evento tiene una `SwipeDirection` propiedad, de tipo `SwipeDirection`.
- `CloseRequested`se desencadena cuando se cierran los elementos de deslizamiento.

Además, `SwipeView` incluye `Open` métodos y `Close` , que abren y cierran los elementos de deslizamiento mediante programación, respectivamente.

> [!NOTE]
> `SwipeView`tiene una plataforma específica para iOS y Android, que controla la transición que se usa al abrir un `SwipeView`. Para obtener más información, consulte el [modo de transición de SwipeView con el dedo en iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) y [SwipeView Deslice el modo de transición en Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Creación de un SwipeView

Un `SwipeView` debe definir el contenido que `SwipeView` rodea y los elementos de deslizamiento que se revelan por el gesto de deslizar rápidamente. Los elementos de deslizamiento son uno `SwipeItem` o más objetos que se colocan en `SwipeView` una de las `LeftItems`cuatro `RightItems`colecciones `TopItems`direccionales `BottomItems`:,, o.

En el ejemplo siguiente se muestra cómo crear una `SwipeView` instancia de en XAML:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

El código de C# equivalente es el siguiente:

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

En este ejemplo, el `SwipeView` contenido es un [`Grid`](xref:Xamarin.Forms.Grid) que contiene [`Label`](xref:Xamarin.Forms.Label):

[![Captura de pantalla del contenido de SwipeView, en iOS y Android](swipeview-images/swipeview-content.png "Contenido de SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenido de SwipeView")

Los elementos de deslizamiento se utilizan para realizar acciones `SwipeView` en el contenido y se revelan cuando el control se desliza rápidamente desde el lado izquierdo:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos, en iOS y Android](swipeview-images/swipeview-swipeitems.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

De forma predeterminada, un elemento de deslizamiento se ejecuta cuando el usuario lo puntea. No obstante, este comportamiento se puede modificar. Para obtener más información, consulte el [modo de deslizar rápidamente](#swipe-mode).

Una vez que se ha ejecutado un dedo, se ocultan los elementos `SwipeView` de deslizamiento y se vuelve a mostrar el contenido. No obstante, este comportamiento se puede modificar. Para obtener más información, consulte el [comportamiento de deslizamiento](#swipe-behavior).

> [!NOTE]
> El deslizamiento de contenido y el deslizamiento de los elementos se pueden colocar en línea o definirse como recursos.

## <a name="swipe-items"></a>Deslizar rápidamente los elementos

Las `LeftItems`colecciones `RightItems`, `TopItems`, y `BottomItems` son de tipo `SwipeItems`. La `SwipeItems` clase define las siguientes propiedades:

- `Mode`, de tipo `SwipeMode`, que indica el efecto de una interacción de deslizar rápidamente. Para obtener más información sobre el modo de deslizar rápidamente, vea el [modo de deslizar rápidamente](#swipe-mode).
- `SwipeBehaviorOnInvoked`, de tipo `SwipeBehaviorOnInvoked`, que indica cómo se `SwipeView` comporta una vez que se invoca un elemento de deslizamiento. Para obtener más información sobre el comportamiento de los deslizamientos, vea [deslizar el comportamiento](#swipe-behavior).

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Cada elemento de deslizamiento se define `SwipeItem` como un objeto que se coloca en una de `SwipeItems` las cuatro colecciones direccionales. La `SwipeItem` clase se deriva de la [`MenuItem`](xref:Xamarin.Forms.MenuItem) clase y agrega los siguientes miembros:

- `BackgroundColor` Propiedad de tipo `Color`que define el color de fondo del elemento de deslizamiento. Esta propiedad está respaldada por una propiedad enlazable.
- Un `Invoked` evento, que se desencadena cuando se ejecuta el elemento de deslizamiento.

> [!IMPORTANT]
> La [`MenuItem`](xref:Xamarin.Forms.MenuItem) clase define varias propiedades, entre `Command`las `CommandParameter`que `IconImageSource`se incluyen `Text`,, y. Estas propiedades se pueden establecer en un `SwipeItem` objeto para definir su apariencia y para definir un `ICommand` que se ejecute cuando se invoque el elemento de deslizamiento. Para obtener más información, consulte [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

En el ejemplo siguiente se `SwipeItem` muestran dos objetos `LeftItems` en la colección `SwipeView`de un objeto:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

La apariencia de cada `SwipeItem` se define mediante una combinación de las `Text`propiedades `IconImageSource`, y `BackgroundColor` :

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos, en iOS y Android](swipeview-images/swipeview-swipeitems.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

Cuando `SwipeItem` se puntea en, su `Invoked` evento se activa y se controla mediante su controlador de eventos registrado. Como alternativa, la `Command` propiedad se puede establecer en una `ICommand` implementación que se ejecutará cuando `SwipeItem` se invoque.

> [!NOTE]
> Cuando se define la apariencia `SwipeItem` de un solo mediante las `Text` propiedades `IconImageSource` o, el contenido siempre se centra.

Además de definir los elementos de deslizar rápidamente como `SwipeItem` objetos, también es posible definir vistas de elemento de deslizamiento personalizado. Para obtener más información, vea [elementos de deslizamiento personalizado](#custom-swipe-items).

## <a name="swipe-direction"></a>Deslizar rápidamente

`SwipeView`admite cuatro direcciones de deslizamiento diferentes, con la dirección de deslizamiento que `SwipeItems` se define `SwipeItem` en la colección direcciona a la que se agregan los objetos. Cada dirección de deslizamiento puede mantener sus propios elementos de deslizamiento. Por ejemplo, en el ejemplo siguiente se `SwipeView` muestra un cuyos elementos de deslizamiento dependen de la dirección de deslizamiento:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

En este ejemplo, el `SwipeView` contenido se puede deslizar hacia la derecha o hacia la izquierda. Al deslizar el dedo hacia la derecha, se mostrará el **elemento de deslizar rápidamente el** dedo, mientras que al pasar a la izquierda se mostrarán los elementos de deslizamiento **favorito** y de **uso compartido** .

> [!WARNING]
> Solo se puede establecer una instancia `SwipeItems` de una colección direccional a la vez en `SwipeView`. Por lo tanto, no puede `LeftItems` tener dos definiciones `SwipeView`en.

Los `SwipeStarted`eventos `SwipeChanging`, y `SwipeEnded` notifican la dirección de deslizamiento `SwipeDirection` a través de la propiedad en los argumentos del evento. Esta propiedad es de tipo `SwipeDirection`, que es una enumeración que consta de cuatro miembros:

- `Right`indica que se ha producido un dedo a la derecha.
- `Left`indica que se ha producido un deslizamiento a la izquierda.
- `Up`indica que se ha producido un deslizamiento hacia arriba.
- `Down`indica que se ha producido un deslizamiento hacia abajo.

## <a name="swipe-mode"></a>Modo de deslizar rápidamente

La `SwipeItems` clase tiene una `Mode` propiedad, que indica el efecto de una interacción de deslizar rápidamente. Esta propiedad debe establecerse en uno de los `SwipeMode` miembros de enumeración:

- `Reveal`indica que un deslizador revela los elementos de deslizamiento. Este es el valor predeterminado de la propiedad `SwipeItems.Mode`.
- `Execute`indica que un dedo ejecuta los elementos de deslizamiento.

En el modo de visualización, el usuario desliza `SwipeView` el dedo para abrir un menú que consta de uno o varios elementos de deslizamiento y debe pulsar explícitamente en un elemento de deslizamiento para ejecutarlo. Una vez que se haya ejecutado el dedo, se cerrarán los elementos `SwipeView` de deslizamiento y se volverá a mostrar el contenido. En el modo de ejecución, el usuario desliza `SwipeView` el dedo para abrir un menú que consta de uno o varios elementos de deslizamiento, que se ejecutan automáticamente. Después de la ejecución, se cierran los elementos `SwipeView` de deslizamiento y se vuelve a mostrar el contenido.

En el ejemplo siguiente se `SwipeView` muestra una configurada para usar el modo de ejecución:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

En este ejemplo, el `SwipeView` contenido se puede deslizar hacia la derecha para mostrar el elemento de deslizar rápidamente, que se ejecuta inmediatamente. Después de la ejecución `SwipeView` , se vuelve a mostrar el contenido.

## <a name="swipe-behavior"></a>Comportamiento de deslizar rápidamente

La `SwipeItems` clase tiene una `SwipeBehaviorOnInvoked` propiedad, que indica cómo se `SwipeView` comporta una vez que se invoca un elemento de deslizamiento. Esta propiedad debe establecerse en uno de los `SwipeBehaviorOnInvoked` miembros de enumeración:

- `Auto`indica que en modo de revelar se `SwipeView` cierra después de invocar un elemento de deslizamiento y en `SwipeView` modo de ejecución, el permanece abierto después de invocar un elemento de deslizamiento. Este es el valor predeterminado de la propiedad `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close`indica que se `SwipeView` cierra después de invocar un elemento de deslizamiento.
- `RemainOpen`indica que `SwipeView` permanece abierto después de invocar un elemento de deslizamiento.

En el ejemplo siguiente se `SwipeView` muestra una configurada para que permanezca abierta después de invocar un elemento de deslizamiento:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>Elementos deslizantes personalizados

Los elementos de deslizamiento personalizado se pueden `SwipeItemView` definir con el tipo. La `SwipeItemView` clase se deriva de la [`ContentView`](xref:Xamarin.Forms.ContentView) clase y agrega las siguientes propiedades:

- `Command`, de tipo `ICommand`, que se ejecuta cuando se puntea un elemento de deslizamiento.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La `SwipeItemView` clase también define un `Invoked` evento que se desencadena cuando se puntea el elemento, después de `Command` que se ejecute.

En el ejemplo siguiente se `SwipeItemView` muestra un objeto `LeftItems` de la colección `SwipeView`de:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

En este ejemplo, el `SwipeItemView` objeto incluye [`StackLayout`](xref:Xamarin.Forms.StackLayout) un objeto [`Entry`](xref:Xamarin.Forms.Entry) que contiene [`Label`](xref:Xamarin.Forms.Label)un objeto y un objeto. Una vez que el usuario escribe la `Entry`entrada en el, se `SwipeViewItem` puede puntear en el resto de `ICommand` , que ejecuta `SwipeItemView.Command` la definida por la propiedad.

## <a name="open-and-close-a-swipeview-programmatically"></a>Abrir y cerrar un SwipeView mediante programación

`SwipeView`incluye `Open` métodos `Close` y, que abren y cierran los elementos de deslizamiento mediante programación, respectivamente.

El `Open` método requiere un `OpenSwipeItem` argumento, para especificar la dirección desde `SwipeView` la que se abrirá. La `OpenSwipeItem` enumeración tiene cuatro miembros:

- `LeftItems`, que indica que `SwipeView` se abrirá a la izquierda para que se muestren los elementos de deslizamiento `LeftItems` en la colección.
- `TopItems`, que indica que `SwipeView` se abrirá desde la parte superior para mostrar los elementos de deslizamiento en la `TopItems` colección.
- `RightItems`, que indica que se `SwipeView` abrirá desde la derecha para mostrar los elementos que se desconectan en `RightItems` la colección.
- `BottomItems`, que indica que `SwipeView` se abrirá desde la parte inferior, para mostrar los elementos de deslizamiento en `BottomItems` la colección.

Dado un `SwipeView` denominado `swipeView`, en el ejemplo siguiente se muestra cómo abrir `SwipeView` un para mostrar los elementos de deslizamiento en la `LeftItems` Colección:

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

A `swipeView` continuación, se puede cerrar con `Close` el método:

```csharp
swipeView.Close();
```

> [!NOTE]
> Cuando se `Close` invoca el método, se desencadena `CloseRequested` el evento.

## <a name="disable-a-swipeview"></a>Deshabilitar un SwipeView

Una aplicación puede entrar en un estado en el que deslizar un elemento de contenido no es una operación válida. En tales casos, se `SwipeView` puede deshabilitar estableciendo su `IsEnabled` propiedad en. `false` Esto impedirá que los usuarios puedan pasar el dedo por el contenido para mostrar los elementos de deslizamiento.

Además, al `Command` definir la propiedad de un `SwipeItem` objeto o `SwipeItemView`, se `CanExecute` `ICommand` puede especificar el delegado de para habilitar o deshabilitar el elemento de deslizar rápidamente.

## <a name="related-links"></a>Vínculos relacionados

- [SwipeView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [MenuItem de Xamarin.Forms](~/xamarin-forms/user-interface/menuitem.md)
