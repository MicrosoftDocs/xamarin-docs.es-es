---
title: SwipeView de Xamarin. Forms
description: Xamarin. Forms SwipeView es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 6131287b200846a033e0c476d7039dfd774cab68
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635594"
---
# <a name="xamarinforms-swipeview"></a>SwipeView de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

El `SwipeView` es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos en una CollectionView, en iOS y Android](swipeview-images/swipeview-collectionview.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

`SwipeView` está disponible en Xamarin. Forms 4,4. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la clase `AppDelegate` en iOS, a la clase `MainActivity` en Android o a la clase `App` en UWP, antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` define las siguientes propiedades:

- `LeftItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo por el control hacia la izquierda.
- `RightItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo por el control hacia la derecha.
- `TopItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando el control se desliza hacia arriba hacia abajo.
- `BottomItems`, de tipo `SwipeItems`, que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo hacia arriba hacia abajo.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Además, el `SwipeView` hereda la propiedad [`Content`](xref:Xamarin.Forms.ContentView.Content) de la clase [`ContentView`](xref:Xamarin.Forms.ContentView) . La propiedad `Content` es la propiedad de contenido de la clase `SwipeView` y, por tanto, no es necesario establecer explícitamente.

La clase `SwipeView` también define cuatro eventos:

- `SwipeStarted` se activa cuando se inicia un dedo. El objeto `SwipeStartedEventArgs` que acompaña a este evento tiene una propiedad `SwipeDirection`, de tipo `SwipeDirection`.
- `SwipeChanging` se desencadena cuando se mueve el dedo. El objeto `SwipeChangingEventArgs` que acompaña a este evento tiene una propiedad `SwipeDirection`, de tipo `SwipeDirection`, y una propiedad `Offset` de tipo `double`.
- `SwipeEnded` se desencadena cuando finaliza un deslizamiento. El objeto `SwipeEndedEventArgs` que acompaña a este evento tiene una propiedad `SwipeDirection`, de tipo `SwipeDirection`.
- `CloseRequested` se desencadena cuando se cierran los elementos de deslizamiento.

Además, `SwipeView` define un método de `Close`, que cierra los elementos de deslizamiento.

> [!NOTE]
> `SwipeView` tiene una plataforma específica para iOS y Android, que controla la transición que se usa al abrir un `SwipeView`. Para obtener más información, consulte el [modo de transición de SwipeView con el dedo en iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) y [SwipeView Deslice el modo de transición en Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Creación de un SwipeView

Un `SwipeView` debe definir el contenido que contenga el `SwipeView` y los elementos que se desplazan por el gesto de deslizar rápidamente. Los elementos de deslizamiento son uno o más objetos `SwipeItem` que se colocan en una de las cuatro colecciones direccionales `SwipeView`: `LeftItems`, `RightItems`, `TopItems`o `BottomItems`.

En el ejemplo siguiente se muestra cómo crear una instancia de un `SwipeView` en XAML:

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

En este ejemplo, el contenido de la `SwipeView` es un [`Grid`](xref:Xamarin.Forms.Grid) que contiene un [`Label`](xref:Xamarin.Forms.Label):

[![Captura de pantalla del contenido de SwipeView, en iOS y Android](swipeview-images/swipeview-content.png "Contenido de SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenido de SwipeView")

Los elementos de deslizamiento se utilizan para realizar acciones en el contenido de la `SwipeView` y se revelan cuando el control se desliza rápidamente desde el lado izquierdo:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos, en iOS y Android](swipeview-images/swipeview-swipeitems.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

De forma predeterminada, un elemento de deslizamiento se ejecuta cuando el usuario lo puntea. No obstante, se puede modificar este comportamiento. Para obtener más información, consulte el [modo de deslizar rápidamente](#swipe-mode).

Una vez que se ha ejecutado un dedo, se ocultan los elementos de deslizamiento y se vuelve a mostrar el contenido de la `SwipeView`. No obstante, se puede modificar este comportamiento. Para obtener más información, consulte el [comportamiento de deslizamiento](#swipe-behavior).

> [!NOTE]
> El deslizamiento de contenido y el deslizamiento de los elementos se pueden colocar en línea o definirse como recursos.

## <a name="swipe-items"></a>Deslizar rápidamente los elementos

Las colecciones `LeftItems`, `RightItems`, `TopItems`y `BottomItems` son de tipo `SwipeItems`. La clase `SwipeItems` define las siguientes propiedades:

- `Mode`, de tipo `SwipeMode`, que indica el efecto de una interacción de deslizar rápidamente. Para obtener más información sobre el modo de deslizar rápidamente, vea el [modo de deslizar rápidamente](#swipe-mode).
- `SwipeBehaviorOnInvoked`, de tipo `SwipeBehaviorOnInvoked`, que indica cómo se comporta un `SwipeView` después de invocar un elemento de deslizamiento. Para obtener más información sobre el comportamiento de los deslizamientos, vea [deslizar el comportamiento](#swipe-behavior).

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Cada elemento de deslizamiento se define como un objeto `SwipeItem` que se coloca en una de las cuatro colecciones direccionales `SwipeItems`. La clase `SwipeItem` se deriva de la clase [`MenuItem`](xref:Xamarin.Forms.MenuItem) y agrega los siguientes miembros:

- `BackgroundColor` propiedad, de tipo `Color`, que define el color de fondo del elemento de deslizamiento. Esta propiedad está respaldada por una propiedad enlazable.
- `Invoked` evento, que se desencadena cuando se ejecuta el elemento de deslizar rápidamente.

> [!IMPORTANT]
> La clase [`MenuItem`](xref:Xamarin.Forms.MenuItem) define varias propiedades, como `Command`, `CommandParameter`, `IconImageSource`y `Text`. Estas propiedades se pueden establecer en un objeto `SwipeItem` para definir su apariencia y para definir una `ICommand` que se ejecuta cuando se invoca el elemento de deslizamiento. Para obtener más información, consulte [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

En el ejemplo siguiente se muestran dos objetos `SwipeItem` en la colección `LeftItems` de un `SwipeView`:

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

La apariencia de cada `SwipeItem` se define mediante una combinación de las propiedades `Text`, `IconImageSource`y `BackgroundColor`:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos, en iOS y Android](swipeview-images/swipeview-swipeitems.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

Cuando se puntea un `SwipeItem`, su evento de `Invoked` se activa y se controla mediante su controlador de eventos registrado. Como alternativa, la propiedad `Command` se puede establecer en una implementación `ICommand` que se ejecutará cuando se invoque la `SwipeItem`.

> [!NOTE]
> Cuando el aspecto de un `SwipeItem` se define solo mediante las propiedades `Text` o `IconImageSource`, el contenido siempre se centra.

Además de definir los elementos de deslizar rápidamente como objetos `SwipeItem`, también es posible definir vistas de elemento de deslizamiento personalizado. Para obtener más información, vea [elementos de deslizamiento personalizado](#custom-swipe-items).

## <a name="swipe-direction"></a>Deslizar rápidamente

`SwipeView` admite cuatro direcciones de deslizamiento diferentes, con la dirección de deslizamiento que se define en la colección de `SwipeItems` direccional a la que se agregan los objetos `SwipeItem`. Cada dirección de deslizamiento puede mantener sus propios elementos de deslizamiento. Por ejemplo, en el ejemplo siguiente se muestra una `SwipeView` cuyos elementos de deslizamiento dependen de la dirección de deslizamiento:

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

En este ejemplo, el contenido del `SwipeView` se puede deslizar hacia la derecha o hacia la izquierda. Al deslizar el dedo hacia la derecha, se mostrará el **elemento de deslizar rápidamente el** dedo, mientras que al pasar a la izquierda se mostrarán los elementos de deslizamiento **favorito** y de **uso compartido** .

> [!WARNING]
> Solo se puede establecer una instancia de una colección de `SwipeItems` direccional a la vez en un `SwipeView`. Por lo tanto, no puede tener dos definiciones de `LeftItems` en un `SwipeView`.

Los eventos `SwipeStarted`, `SwipeChanging`y `SwipeEnded` notifican la dirección de deslizar rápidamente a través de la propiedad `SwipeDirection` en los argumentos del evento. Esta propiedad es de tipo `SwipeDirection`, que es una enumeración que consta de cuatro miembros:

- `Right` indica que se ha producido un deslizamiento a la derecha.
- `Left` indica que se ha producido un deslizamiento a la izquierda.
- `Up` indica que se ha producido un deslizamiento hacia arriba.
- `Down` indica que se ha producido un deslizamiento hacia abajo.

## <a name="swipe-mode"></a>Modo de deslizar rápidamente

La clase `SwipeItems` tiene una propiedad `Mode`, que indica el efecto de una interacción de deslizar rápidamente. Esta propiedad debe establecerse en uno de los miembros de enumeración de `SwipeMode`:

- `Reveal` indica que un deslizamiento revela los elementos de deslizamiento. Este es el valor predeterminado de la propiedad `SwipeItems.Mode`.
- `Execute` indica que un dedo ejecuta los elementos de deslizamiento.

En el modo de visualización, el usuario desliza el dedo por un `SwipeView` para abrir un menú que consta de uno o varios elementos de deslizar rápidamente y debe pulsar explícitamente en un elemento de deslizamiento para ejecutarlo. Una vez que se haya ejecutado el dedo, se cerrarán los elementos de deslizamiento y se volverá a mostrar el contenido de la `SwipeView`. En el modo de ejecución, el usuario desliza el dedo por un `SwipeView` para abrir un menú que consta de uno o varios elementos de deslizamiento, que se ejecutan automáticamente. Después de la ejecución, se cierran los elementos de deslizamiento y se vuelve a mostrar el contenido de la `SwipeView`.

En el ejemplo siguiente se muestra un `SwipeView` configurado para usar el modo de ejecución:

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

En este ejemplo, el contenido del `SwipeView` se puede deslizar hacia la derecha para mostrar el elemento de deslizar rápidamente, que se ejecuta inmediatamente. Después de la ejecución, se vuelve a mostrar el contenido de la `SwipeView`.

## <a name="swipe-behavior"></a>Comportamiento de deslizar rápidamente

La clase `SwipeItems` tiene una propiedad `SwipeBehaviorOnInvoked`, que indica cómo se comporta un `SwipeView` después de invocar un elemento de deslizamiento. Esta propiedad debe establecerse en uno de los miembros de enumeración de `SwipeBehaviorOnInvoked`:

- `Auto` indica que en el modo de visualización el `SwipeView` se cierra después de invocar un elemento de deslizamiento y en modo de ejecución, el `SwipeView` permanece abierto después de invocar un elemento de deslizamiento. Este es el valor predeterminado de la propiedad `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close` indica que el `SwipeView` se cierra después de invocar un elemento de deslizamiento.
- `RemainOpen` indica que el `SwipeView` permanece abierto después de invocar un elemento de deslizamiento.

En el ejemplo siguiente se muestra un `SwipeView` configurado para permanecer abierto después de invocar un elemento de deslizamiento:

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

Los elementos de deslizamiento personalizado se pueden definir con el tipo de `SwipeItemView`. La clase `SwipeItemView` se deriva de la clase [`ContentView`](xref:Xamarin.Forms.ContentView) y agrega las siguientes propiedades:

- `Command`, de tipo `ICommand`, que se ejecuta cuando se puntea un elemento de deslizamiento.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La clase `SwipeItemView` también define un evento `Invoked` que se desencadena cuando se puntea el elemento, después de que se ejecute la `Command`.

En el ejemplo siguiente se muestra un objeto `SwipeItemView` en la colección `LeftItems` de un `SwipeView`:

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

En este ejemplo, el `SwipeItemView` consta de un [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contiene un [`Entry`](xref:Xamarin.Forms.Entry) y un [`Label`](xref:Xamarin.Forms.Label). Una vez que el usuario escribe la entrada en el `Entry`, se puede puntear en el resto del `SwipeViewItem` que ejecuta el `ICommand` definido por la propiedad `SwipeItemView.Command`.

## <a name="disable-a-swipeview"></a>Deshabilitar un SwipeView

Una aplicación puede entrar en un estado en el que deslizar un elemento de contenido no es una operación válida. En tales casos, el `SwipeView` se puede deshabilitar estableciendo su propiedad `IsEnabled` en `false`. Esto impedirá que los usuarios puedan pasar el dedo por el contenido para mostrar los elementos de deslizamiento.

Además, al definir la propiedad `Command` de una `SwipeItem` o `SwipeItemView`, se puede especificar el delegado `CanExecute` del `ICommand` para habilitar o deshabilitar el elemento de deslizar rápidamente.

## <a name="related-links"></a>Vínculos relacionados

- [SwipeView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [MenuItem de Xamarin.Forms](~/xamarin-forms/user-interface/menuitem.md)
