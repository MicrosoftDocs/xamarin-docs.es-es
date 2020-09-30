---
title: Xamarin.Forms SwipeView
description: Xamarin.FormsSwipeView es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 178d36eabfdbe0452bec456979fcca89acdf8926
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563346"
---
# <a name="no-locxamarinforms-swipeview"></a>Xamarin.Forms SwipeView

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView`Es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos en una CollectionView, en iOS y Android](swipeview-images/swipeview-collectionview.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

`SwipeView` está disponible en Xamarin.Forms 4,4. Sin embargo, actualmente es experimental y solo se puede usar agregando la siguiente línea de código a la `AppDelegate` clase en iOS, a la `MainActivity` clase en Android o a la `App` clase en UWP, antes de llamar a `Forms.Init` :

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` define las siguientes propiedades:

- `LeftItems`, de tipo `SwipeItems` , que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo por el control hacia la izquierda.
- `RightItems`, de tipo `SwipeItems` , que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo por el control hacia la derecha.
- `TopItems`, de tipo `SwipeItems` , que representa los elementos de deslizamiento que se pueden invocar cuando el control se desliza hacia arriba hacia abajo.
- `BottomItems`, de tipo `SwipeItems` , que representa los elementos de deslizamiento que se pueden invocar cuando se pasa el dedo hacia arriba en el control.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

Además, `SwipeView` hereda la [`Content`](xref:Xamarin.Forms.ContentView.Content) propiedad de la [`ContentView`](xref:Xamarin.Forms.ContentView) clase. La `Content` propiedad es la propiedad de contenido de la `SwipeView` clase y, por tanto, no es necesario establecer explícitamente.

La `SwipeView` clase también define cuatro eventos:

- `SwipeStarted` se desencadena cuando se inicia un deslizamiento. El `SwipeStartedEventArgs` objeto que acompaña a este evento tiene una `SwipeDirection` propiedad, de tipo `SwipeDirection` .
- `SwipeChanging` se desencadena cuando se mueve el dedo. El `SwipeChangingEventArgs` objeto que acompaña a este evento tiene una `SwipeDirection` propiedad, de tipo `SwipeDirection` y una `Offset` propiedad de tipo `double` .
- `SwipeEnded` se desencadena cuando finaliza un deslizamiento. El `SwipeEndedEventArgs` objeto que acompaña a este evento tiene una `SwipeDirection` propiedad, de tipo `SwipeDirection` .
- `CloseRequested` se desencadena cuando se cierran los elementos de deslizamiento.

Además, `SwipeView` incluye `Open` métodos y `Close` , que abren y cierran los elementos de deslizamiento mediante programación, respectivamente.

> [!NOTE]
> `SwipeView` tiene una plataforma específica para iOS y Android, que controla la transición que se usa al abrir un `SwipeView` . Para obtener más información, consulte el [modo de transición de SwipeView con el dedo en iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) y [SwipeView Deslice el modo de transición en Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Creación de un SwipeView

Un `SwipeView` debe definir el contenido que `SwipeView` rodea y los elementos de deslizamiento que se revelan por el gesto de deslizar rápidamente. Los elementos de deslizamiento son uno o más `SwipeItem` objetos que se colocan en una de las cuatro `SwipeView` colecciones direccionales: `LeftItems` , `RightItems` , `TopItems` o `BottomItems` .

En el ejemplo siguiente se muestra cómo crear una instancia `SwipeView` de en XAML:

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

En este ejemplo, el `SwipeView` contenido es un [`Grid`](xref:Xamarin.Forms.Grid) que contiene [`Label`](xref:Xamarin.Forms.Label) :

[![Captura de pantalla del contenido de SwipeView, en iOS y Android](swipeview-images/swipeview-content.png "Contenido de SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenido de SwipeView")

Los elementos de deslizamiento se utilizan para realizar acciones en el `SwipeView` contenido y se revelan cuando el control se desliza rápidamente desde el lado izquierdo:

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos, en iOS y Android](swipeview-images/swipeview-swipeitems.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

De forma predeterminada, un elemento de deslizamiento se ejecuta cuando el usuario lo puntea. No obstante, se puede modificar este comportamiento. Para obtener más información, consulte el [modo de deslizar rápidamente](#swipe-mode).

Una vez que se ha ejecutado un dedo, se ocultan los elementos de deslizamiento y `SwipeView` se vuelve a mostrar el contenido. No obstante, se puede modificar este comportamiento. Para obtener más información, consulte el [comportamiento de deslizamiento](#swipe-behavior).

> [!NOTE]
> El deslizamiento de contenido y el deslizamiento de los elementos se pueden colocar en línea o definirse como recursos.

## <a name="swipe-items"></a>Deslizar rápidamente los elementos

Las `LeftItems` `RightItems` colecciones,, `TopItems` y `BottomItems` son de tipo `SwipeItems` . La clase `SwipeItems` define las propiedades siguientes:

- `Mode`, de tipo `SwipeMode` , que indica el efecto de una interacción de deslizar rápidamente. Para obtener más información sobre el modo de deslizar rápidamente, vea el [modo de deslizar rápidamente](#swipe-mode).
- `SwipeBehaviorOnInvoked`, de tipo `SwipeBehaviorOnInvoked` , que indica cómo `SwipeView` se comporta una vez que se invoca un elemento de deslizamiento. Para obtener más información sobre el comportamiento de los deslizamientos, vea [deslizar el comportamiento](#swipe-behavior).

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

Cada elemento de deslizamiento se define como un `SwipeItem` objeto que se coloca en una de las cuatro `SwipeItems` colecciones direccionales. La `SwipeItem` clase se deriva de la [`MenuItem`](xref:Xamarin.Forms.MenuItem) clase y agrega los siguientes miembros:

- `BackgroundColor`Propiedad de tipo `Color` que define el color de fondo del elemento de deslizamiento. Esta propiedad está respaldada por una propiedad enlazable.
- Un `Invoked` evento, que se desencadena cuando se ejecuta el elemento de deslizamiento.

> [!IMPORTANT]
> La [`MenuItem`](xref:Xamarin.Forms.MenuItem) clase define varias propiedades, entre las que se incluyen `Command` ,, `CommandParameter` `IconImageSource` y `Text` . Estas propiedades se pueden establecer en un `SwipeItem` objeto para definir su apariencia y para definir un `ICommand` que se ejecute cuando se invoque el elemento de deslizamiento. Para obtener más información, vea [ Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

En el ejemplo siguiente se muestran dos `SwipeItem` objetos en la `LeftItems` colección de un objeto `SwipeView` :

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

La apariencia de cada `SwipeItem` se define mediante una combinación de las `Text` `IconImageSource` propiedades, y `BackgroundColor` :

[![Captura de pantalla de SwipeView deslizar rápidamente los elementos, en iOS y Android](swipeview-images/swipeview-swipeitems.png "SwipeView deslizar rápidamente los elementos")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView deslizar rápidamente los elementos")

Cuando `SwipeItem` se puntea en, su `Invoked` evento se activa y se controla mediante su controlador de eventos registrado. Como alternativa, la `Command` propiedad se puede establecer en una `ICommand` implementación que se ejecutará cuando `SwipeItem` se invoque.

> [!NOTE]
> Cuando se define la apariencia de un `SwipeItem` solo mediante las `Text` `IconImageSource` propiedades o, el contenido siempre se centra.

Además de definir los elementos de deslizar rápidamente como `SwipeItem` objetos, también es posible definir vistas de elemento de deslizamiento personalizado. Para obtener más información, vea [elementos de deslizamiento personalizado](#custom-swipe-items).

## <a name="swipe-direction"></a>Deslizar rápidamente

`SwipeView` admite cuatro direcciones de deslizamiento diferentes, con la dirección de deslizamiento que se define en la colección direcciona `SwipeItems` a la que `SwipeItem` se agregan los objetos. Cada dirección de deslizamiento puede mantener sus propios elementos de deslizamiento. Por ejemplo, en el ejemplo siguiente se muestra un `SwipeView` cuyos elementos de deslizamiento dependen de la dirección de deslizamiento:

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
> Solo se puede establecer una instancia de una `SwipeItems` colección direccional a la vez en `SwipeView` . Por lo tanto, no puede tener dos `LeftItems` definiciones en `SwipeView` .

Los `SwipeStarted` `SwipeChanging` eventos, y `SwipeEnded` notifican la dirección de deslizamiento a través de la `SwipeDirection` propiedad en los argumentos del evento. Esta propiedad es de tipo `SwipeDirection` , que es una enumeración que consta de cuatro miembros:

- `Right` indica que se ha producido un dedo a la derecha.
- `Left` indica que se ha producido un deslizamiento a la izquierda.
- `Up` indica que se ha producido un deslizamiento hacia arriba.
- `Down` indica que se ha producido un deslizamiento hacia abajo.

## <a name="swipe-mode"></a>Modo de deslizar rápidamente

La `SwipeItems` clase tiene una `Mode` propiedad, que indica el efecto de una interacción de deslizar rápidamente. Esta propiedad debe establecerse en uno de los `SwipeMode` miembros de enumeración:

- `Reveal` indica que un deslizador revela los elementos de deslizamiento. Este es el valor predeterminado de la propiedad `SwipeItems.Mode`.
- `Execute` indica que un dedo ejecuta los elementos de deslizamiento.

En el modo de visualización, el usuario desliza el dedo `SwipeView` para abrir un menú que consta de uno o varios elementos de deslizamiento y debe pulsar explícitamente en un elemento de deslizamiento para ejecutarlo. Una vez que se haya ejecutado el dedo, se cerrarán los elementos de deslizamiento y `SwipeView` se volverá a mostrar el contenido. En el modo de ejecución, el usuario desliza el dedo `SwipeView` para abrir un menú que consta de uno o varios elementos de deslizamiento, que se ejecutan automáticamente. Después de la ejecución, se cierran los elementos de deslizamiento y `SwipeView` se vuelve a mostrar el contenido.

En el ejemplo siguiente se muestra una `SwipeView` configurada para usar el modo de ejecución:

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

En este ejemplo, el `SwipeView` contenido se puede deslizar hacia la derecha para mostrar el elemento de deslizar rápidamente, que se ejecuta inmediatamente. Después de la ejecución, `SwipeView` se vuelve a mostrar el contenido.

## <a name="swipe-behavior"></a>Comportamiento de deslizar rápidamente

La `SwipeItems` clase tiene una `SwipeBehaviorOnInvoked` propiedad, que indica cómo `SwipeView` se comporta una vez que se invoca un elemento de deslizamiento. Esta propiedad debe establecerse en uno de los `SwipeBehaviorOnInvoked` miembros de enumeración:

- `Auto` indica que en modo de revelar `SwipeView` se cierra después de invocar un elemento de deslizamiento y en modo de ejecución, el `SwipeView` permanece abierto después de invocar un elemento de deslizamiento. Este es el valor predeterminado de la propiedad `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close` indica que `SwipeView` se cierra después de invocar un elemento de deslizamiento.
- `RemainOpen` indica que `SwipeView` permanece abierto después de invocar un elemento de deslizamiento.

En el ejemplo siguiente se muestra una `SwipeView` configurada para que permanezca abierta después de invocar un elemento de deslizamiento:

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

Los elementos de deslizamiento personalizado se pueden definir con el `SwipeItemView` tipo. La `SwipeItemView` clase se deriva de la [`ContentView`](xref:Xamarin.Forms.ContentView) clase y agrega las siguientes propiedades:

- `Command`, de tipo `ICommand` , que se ejecuta cuando se puntea un elemento de deslizamiento.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

La `SwipeItemView` clase también define un `Invoked` evento que se desencadena cuando se puntea el elemento, después de que `Command` se ejecute.

En el ejemplo siguiente se muestra un `SwipeItemView` objeto de la `LeftItems` colección de `SwipeView` :

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

En este ejemplo, el objeto `SwipeItemView` incluye un objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contiene un objeto [`Entry`](xref:Xamarin.Forms.Entry) y un objeto [`Label`](xref:Xamarin.Forms.Label) . Una vez que el usuario escribe la entrada en el `Entry` , se puede puntear en el resto de, `SwipeViewItem` que ejecuta la `ICommand` definida por la `SwipeItemView.Command` propiedad.

## <a name="open-and-close-a-swipeview-programmatically"></a>Abrir y cerrar un SwipeView mediante programación

`SwipeView` incluye `Open` `Close` métodos y, que abren y cierran los elementos de deslizamiento mediante programación, respectivamente.

El `Open` método requiere un `OpenSwipeItem` argumento, para especificar la dirección desde la que se `SwipeView` abrirá. La `OpenSwipeItem` enumeración tiene cuatro miembros:

- `LeftItems`, que indica que se `SwipeView` abrirá a la izquierda para que se muestren los elementos de deslizamiento en la `LeftItems` colección.
- `TopItems`, que indica que se `SwipeView` abrirá desde la parte superior para mostrar los elementos de deslizamiento en la `TopItems` colección.
- `RightItems`, que indica que se `SwipeView` abrirá desde la derecha para mostrar los elementos que se desconectan en la `RightItems` colección.
- `BottomItems`, que indica que se `SwipeView` abrirá desde la parte inferior, para mostrar los elementos de deslizamiento en `BottomItems` la colección.

Dado un `SwipeView` denominado `swipeView` , en el ejemplo siguiente se muestra cómo abrir un `SwipeView` para mostrar los elementos de deslizamiento en la `LeftItems` colección:

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

`swipeView`A continuación, se puede cerrar con el `Close` método:

```csharp
swipeView.Close();
```

> [!NOTE]
> Cuando `Close` se invoca el método, `CloseRequested` se desencadena el evento.

## <a name="disable-a-swipeview"></a>Deshabilitar un SwipeView

Una aplicación puede entrar en un estado en el que deslizar un elemento de contenido no es una operación válida. En tales casos, `SwipeView` se puede deshabilitar estableciendo su `IsEnabled` propiedad en `false` . Esto impedirá que los usuarios puedan pasar el dedo por el contenido para mostrar los elementos de deslizamiento.

Además, al definir la `Command` propiedad de un `SwipeItem` objeto o `SwipeItemView` , `CanExecute` se puede especificar el delegado de `ICommand` para habilitar o deshabilitar el elemento de deslizar rápidamente.

## <a name="related-links"></a>Vínculos relacionados

- [SwipeView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms Objetos](~/xamarin-forms/user-interface/menuitem.md)