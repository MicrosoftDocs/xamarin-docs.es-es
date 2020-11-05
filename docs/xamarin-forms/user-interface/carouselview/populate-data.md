---
title: Xamarin.Forms Datos de CarouselView
description: Un CarouselView se rellena con datos estableciendo su propiedad ItemsSource en cualquier colección que implementa IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dd3672141382c4b68ccaa90511f184b3ad875411
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365767"
---
# <a name="no-locxamarinforms-carouselview-data"></a>Xamarin.Forms Datos de CarouselView

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) incluye las siguientes propiedades que definen los datos que se van a mostrar y su apariencia:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de tipo `IEnumerable` , especifica la colección de elementos que se van a mostrar y tiene un valor predeterminado de `null` .
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , especifica la plantilla que se va a aplicar a cada elemento de la colección de elementos que se va a mostrar.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) define una `ItemsUpdatingScrollMode` propiedad que representa el comportamiento de desplazamiento de `CarouselView` cuando se agregan nuevos elementos a él. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) admite la virtualización de datos incrementales a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Rellenar un CarouselView con datos

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en cualquier colección que implementa `IEnumerable` . De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) muestra los elementos horizontalmente.

> [!IMPORTANT]
> Si [`CarouselView`](xref:Xamarin.Forms.CarouselView) es necesario actualizar a medida que se agregan, quitan o cambian elementos en la colección subyacente, la colección subyacente debe ser una `IEnumerable` colección que envíe notificaciones de cambios de propiedades, como `ObservableCollection` .

[`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede rellenar con datos mediante el enlace de datos para enlazar su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad a una `IEnumerable` colección. En XAML, esto se consigue con la `Binding` extensión de marcado:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

En este ejemplo, los [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) datos de la propiedad se enlazan a la `Monkeys` propiedad del ViewModel conectado.

> [!NOTE]
> Los enlaces compilados se pueden habilitar para mejorar el rendimiento del enlace de datos en Xamarin.Forms las aplicaciones. Para obtener más información, vea [Enlaces compilados](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obtener información sobre cómo cambiar la [`CarouselView`](xref:Xamarin.Forms.CarouselView) orientación, vea [ Xamarin.Forms diseño de CarouselView](layout.md). Para obtener información sobre cómo definir la apariencia de cada elemento en `CarouselView` , vea [definir la apariencia del elemento](#define-item-appearance). Para obtener más información sobre el enlace de datos, consulte [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento en [`CarouselView`](xref:Xamarin.Forms.CarouselView) puede definirse estableciendo la [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    Label nameLabel = new Label { ... };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Image image = new Image { ... };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label locationLabel = new Label { ... };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Label detailsLabel = new Label { ... };
    detailsLabel.SetBinding(Label.TextProperty, "Details");

    StackLayout stackLayout = new StackLayout
    {
        Children = { nameLabel, image, locationLabel, detailsLabel }
    };

    Frame frame = new Frame { ... };
    StackLayout rootStackLayout = new StackLayout
    {
        Children = { frame }
    };

    return rootStackLayout;
});
```

Los elementos especificados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento en `CarouselView` . En el ejemplo, el diseño dentro de `DataTemplate` se administra mediante un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , y los datos se muestran con un [`Image`](xref:Xamarin.Forms.Image) objeto y tres [`Label`](xref:Xamarin.Forms.Label) objetos, que se enlazan a las propiedades de la `Monkey` clase:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Las siguientes capturas de pantallas muestran el resultado de la plantilla de cada elemento:

[![Captura de pantalla de CarouselView donde cada elemento tiene una plantilla, en iOS y Android](populate-data-images/datatemplate.png "Elementos con plantilla en un CarouselView")](populate-data-images/datatemplate-large.png#lightbox "Elementos con plantilla en un CarouselView")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento en [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede elegir en tiempo de ejecución, según el valor del elemento, estableciendo la [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls"
             x:Class="CarouselViewDemos.Views.HorizontalLayoutDataTemplateSelectorPage">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CarouselView ItemsSource="{Binding Monkeys}"
                  ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad se establece en un `MonkeyDataTemplateSelector` objeto. En el ejemplo siguiente se muestra la `MonkeyDataTemplateSelector` clase:

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

La `MonkeyDataTemplateSelector` clase define `AmericanMonkey` `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) las propiedades y que se establecen en distintas plantillas de datos. La `OnSelectTemplate` invalidación devuelve la `AmericanMonkey` plantilla cuando el nombre de Monkey contiene "America". Cuando el nombre de Monkey no contiene "America", la `OnSelectTemplate` invalidación devuelve la `OtherMonkey` plantilla, que muestra los datos atenuados:

[![Captura de pantalla de selección de plantilla de elemento de tiempo de ejecución de CarouselView, en iOS y Android](populate-data-images/datatemplateselector.png "Selección de plantilla de elementos en tiempo de ejecución en un CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Selección de plantilla de elementos en tiempo de ejecución en un CarouselView")

Para más información sobre los selectores de plantilla de datos, consulte [creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Al utilizar [`CarouselView`](xref:Xamarin.Forms.CarouselView) , no establezca nunca el elemento raíz de los [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos en un `ViewCell` . Esto hará que se produzca una excepción porque `CarouselView` no tiene concepto de celdas.

## <a name="display-indicators"></a>presentación de indicadores

Los indicadores, que representan el número de elementos y la posición actual en un `CarouselView` , se pueden mostrar junto a `CarouselView` . Esto puede hacerse con el `IndicatorView` control:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

En este ejemplo, `IndicatorView` se representa debajo de `CarouselView` , con un indicador para cada elemento en `CarouselView` . `IndicatorView`Se rellena con datos estableciendo la `CarouselView.IndicatorView` propiedad en el `IndicatorView` objeto. Cada indicador es un círculo gris claro, mientras que el indicador que representa el elemento actual del `CarouselView` es gris oscuro:

[![Captura de pantalla de CarouselView y IndicatorView en iOS y Android](populate-data-images/indicators.png "Círculos IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos IndicatorView")

> [!IMPORTANT]
> Al establecer la propiedad `CarouselView.IndicatorView` , se obtiene el enlace de la `IndicatorView.Position` propiedad a la `CarouselView.Position` propiedad y la `IndicatorView.ItemsSource` propiedad se enlaza a la `CarouselView.ItemsSource` propiedad.

Para obtener más información acerca de los indicadores, vea [ Xamarin.Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md).

## <a name="context-menus"></a>Menús contextuales

[`CarouselView`](xref:Xamarin.Forms.CarouselView) admite menús contextuales para elementos de datos a través de `SwipeView` , que revela el menú contextual con un gesto de deslizar rápidamente. `SwipeView`Es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual para ese elemento de contenido. Por lo tanto, los menús contextuales se implementan para un `CarouselView` mediante la creación de un `SwipeView` que define el contenido que `SwipeView` contiene y los elementos de menú contextual revelados por el gesto de deslizar rápidamente. Esto se logra agregando `SwipeView` a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de cada elemento de datos en `CarouselView` :

```xaml
<CarouselView x:Name="carouselView"
              ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                    <Frame HasShadow="True"
                           BorderColor="DarkGray"
                           CornerRadius="5"
                           Margin="20"
                           HeightRequest="300"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand">
                        <SwipeView>
                            <SwipeView.TopItems>
                                <SwipeItems>
                                    <SwipeItem Text="Favorite"
                                               IconImageSource="favorite.png"
                                               BackgroundColor="LightGreen"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.FavoriteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.TopItems>
                            <SwipeView.BottomItems>
                                <SwipeItems>
                                    <SwipeItem Text="Delete"
                                               IconImageSource="delete.png"
                                               BackgroundColor="LightPink"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.DeleteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.BottomItems>
                            <StackLayout>
                                <!-- Define item appearance -->
                            </StackLayout>
                        </SwipeView>
                    </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    StackLayout stackLayout = new StackLayout();
    Frame frame = new Frame { ... };

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: carouselView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: carouselView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.TopItems = new SwipeItems { favoriteSwipeItem };
    swipeView.BottomItems = new SwipeItems { deleteSwipeItem };

    StackLayout swipeViewStackLayout = new StackLayout { ... };
    swipeView.Content = swipeViewStackLayout;
    frame.Content = swipeView;
    stackLayout.Children.Add(frame);

    return stackLayout;
});
```

En este ejemplo, el `SwipeView` contenido es un [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define la apariencia de cada elemento rodeado por un [`Frame`](xref:Xamarin.Forms.Frame) en [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Los elementos de deslizamiento se utilizan para realizar acciones en el `SwipeView` contenido y se revelan cuando el control se desliza rápidamente desde la parte superior y desde la parte inferior:

[![Captura de pantalla del elemento de menú contextual de CarouselView inferior, en iOS y Android](populate-data-images/swipeview-bottom.png "CarouselView con el elemento de menú contextual SwipeView inferior")](populate-data-images/swipeview-bottom-large.png#lightbox "CarouselView con el elemento de menú contextual SwipeView inferior") 
 [ ![Captura de pantalla del elemento de menú superior de CarouselView, en iOS y Android](populate-data-images/swipeview-top.png "CarouselView con el elemento de menú contextual principal de SwipeView")](populate-data-images/swipeview-top-large.png#lightbox "CarouselView con el elemento de menú contextual principal de SwipeView")

`SwipeView` admite cuatro direcciones de deslizamiento diferentes, con la dirección de deslizamiento que se define en la colección direcciona `SwipeItems` a la que `SwipeItems` se agregan los objetos. De forma predeterminada, se ejecuta un dedo al puntear en el usuario. Además, una vez que se ha ejecutado un dedo, se ocultan los elementos de deslizamiento y `SwipeView` se vuelve a mostrar el contenido. Sin embargo, estos comportamientos se pueden cambiar.

Para obtener más información sobre el `SwipeView` control, vea [ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Extraer para actualizar

[`CarouselView`](xref:Xamarin.Forms.CarouselView) admite la funcionalidad de extracción para actualizar a través de `RefreshView` , lo que permite que los datos que se muestran se actualicen al desplazarse por los elementos. `RefreshView`Es un control contenedor que proporciona la funcionalidad de extracción para actualizar a su elemento secundario, siempre que el elemento secundario admita contenido desplazable. Por lo tanto, la extracción de la actualización se implementa para una `CarouselView` al establecerla como el elemento secundario de un `RefreshView` :

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CarouselView ItemsSource="{Binding Animals}">
        ...
    </CarouselView>
</RefreshView>
```

El código de C# equivalente es el siguiente:

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = carouselView;
// ...
```

Cuando el usuario inicia una actualización, `ICommand` se ejecuta la definida por la `Command` propiedad, que debe actualizar los elementos que se muestran. Se muestra una visualización de actualización mientras se produce la actualización, que consta de un círculo de progreso animado:

[![Captura de pantalla de incorporación de cambios a la actualización de CarouselView en iOS y Android](populate-data-images/pull-to-refresh.png "Incorporación de cambios a la actualización de CarouselView")](populate-data-images/pull-to-refresh-large.png#lightbox "Incorporación de cambios a la actualización de CarouselView")

El valor de la `RefreshView.IsRefreshing` propiedad indica el estado actual de `RefreshView` . Cuando el usuario desencadena una actualización, esta propiedad pasará automáticamente a `true` . Una vez finalizada la actualización, debe restablecer la propiedad a `false` .

Para obtener más información sobre `RefreshView` , vea [ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Cargar datos incrementalmente

[`CarouselView`](xref:Xamarin.Forms.CarouselView) admite la virtualización de datos incrementales a medida que el usuario se desplaza. Esto permite escenarios como la carga asincrónica de una página de datos de un servicio Web, a medida que el usuario se desplaza. Además, el punto en el que se cargan más datos se puede configurar para que los usuarios no vean el espacio en blanco o se detengan del desplazamiento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades para controlar la carga incremental de datos:

- `RemainingItemsThreshold`, de tipo `int` , el umbral de elementos que todavía no están visibles en la lista en la que se `RemainingItemsThresholdReached` desencadenará el evento.
- `RemainingItemsThresholdReachedCommand`, de tipo `ICommand` , que se ejecuta cuando `RemainingItemsThreshold` se alcanza el.
- `RemainingItemsThresholdReachedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también define un `RemainingItemsThresholdReached` evento que se desencadena cuando `CarouselView` se desplaza lo suficiente como para que `RemainingItemsThreshold` no se muestren los elementos. Este evento se puede controlar para cargar más elementos. Además, cuando `RemainingItemsThresholdReached` se desencadena el evento, `RemainingItemsThresholdReachedCommand` se ejecuta, lo que permite que la carga de datos incrementales se realice en un ViewModel.

El valor predeterminado de la `RemainingItemsThreshold` propiedad es-1, lo que indica que el `RemainingItemsThresholdReached` evento nunca se desencadenará. Cuando el valor de la propiedad es 0, el `RemainingItemsThresholdReached` evento se desencadena cuando se muestra el último elemento de [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) . En el caso de valores mayores que 0, el `RemainingItemsThresholdReached` evento se desencadena cuando `ItemsSource` contiene ese número de elementos a los que todavía no se ha desplazado.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) valida la `RemainingItemsThreshold` propiedad de modo que su valor sea siempre mayor o igual que-1.

En el siguiente ejemplo de XAML se muestra un [`CarouselView`](xref:Xamarin.Forms.CarouselView) que carga datos incrementalmente:

```xaml
<CarouselView ItemsSource="{Binding Animals}"
              RemainingItemsThreshold="2"
              RemainingItemsThresholdReached="OnCarouselViewRemainingItemsThresholdReached"
              RemainingItemsThresholdReachedCommand="{Binding LoadMoreDataCommand}">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    RemainingItemsThreshold = 2
};
carouselView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

En este ejemplo de código, el `RemainingItemsThresholdReached` evento se desencadena cuando hay dos elementos que todavía no se han desplazado a y, en respuesta, ejecuta el `OnCollectionViewRemainingItemsThresholdReached` controlador de eventos:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Los datos también se pueden cargar incrementalmente enlazando el `RemainingItemsThresholdReachedCommand` a una `ICommand` implementación de ViewModel.

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms Plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)