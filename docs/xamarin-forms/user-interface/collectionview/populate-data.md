---
title: Datos de CollectionView de Xamarin. Forms
description: Un CollectionView se rellena con datos estableciendo su propiedad ItemsSource en cualquier colección que implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 9442f7878d9290946fabb7bfc5dee77a828228c7
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305934"
---
# <a name="xamarinforms-collectionview-data"></a>Datos de CollectionView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) incluye las siguientes propiedades que definen los datos que se van a mostrar y su apariencia:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de tipo `IEnumerable`, especifica la colección de elementos que se van a mostrar y tiene un valor predeterminado de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica la plantilla que se va a aplicar a cada elemento de la colección de elementos que se va a mostrar.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) define una propiedad `ItemsUpdatingScrollMode` que representa el comportamiento de desplazamiento de la `CollectionView` cuando se agregan nuevos elementos a ella. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Rellenar un CollectionView con datos

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) en cualquier colección que implementa `IEnumerable`. Los elementos se pueden agregar en XAML inicializando la propiedad `ItemsSource` de una matriz de cadenas:

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> Tenga en cuenta que el elemento `x:Array` requiere un atributo `Type` que indica el tipo de los elementos de la matriz.

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) producirá una excepción si su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) se actualiza fuera del subproceso de la interfaz de usuario.

De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra los elementos en una lista vertical, tal como se muestra en las siguientes capturas de pantallas:

[![Captura de pantalla de CollectionView que contiene elementos de texto, en iOS y Android](populate-data-images/text.png "Elementos de texto de una CollectionView")](populate-data-images/text-large.png#lightbox "Elementos de texto de una CollectionView")

> [!IMPORTANT]
> Si es necesario actualizar el [`CollectionView`](xref:Xamarin.Forms.CollectionView) a medida que se agregan, quitan o cambian elementos en la colección subyacente, la colección subyacente debe ser una colección `IEnumerable` que envíe notificaciones de cambios de propiedades, como `ObservableCollection`.

Para obtener información sobre cómo cambiar el diseño de [`CollectionView`](xref:Xamarin.Forms.CollectionView) , consulte [diseño de CollectionView de Xamarin. Forms](layout.md). Para obtener información sobre cómo definir la apariencia de cada elemento en el `CollectionView`, consulte [definir la apariencia del elemento](#define-item-appearance).

### <a name="data-binding"></a>Enlace de datos

[`CollectionView`](xref:Xamarin.Forms.CollectionView) se pueden rellenar con datos mediante el enlace de datos para enlazar su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a una colección `IEnumerable`. En XAML, esto se consigue con la extensión de marcado `Binding`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

En este ejemplo, los datos de la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) se enlazan a la propiedad `Monkeys` del ViewModel conectado.

> [!NOTE]
> Los enlaces compilados se pueden habilitar para mejorar el rendimiento del enlace de datos en aplicaciones de Xamarin. Forms. Para obtener más información, vea [Enlaces compilados](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento del [`CollectionView`](xref:Xamarin.Forms.CollectionView) se puede definir estableciendo la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

Los elementos especificados en la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento de la lista. En el ejemplo, el diseño dentro del `DataTemplate` se administra mediante un [`Grid`](xref:Xamarin.Forms.Grid). El `Grid` contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y dos objetos [`Label`](xref:Xamarin.Forms.Label) , que se enlazan a las propiedades de la clase `Monkey`:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Las siguientes capturas de pantallas muestran el resultado de la plantilla de cada elemento de la lista:

[![Captura de pantalla de CollectionView donde cada elemento tiene una plantilla, en iOS y Android](populate-data-images/datatemplate.png "Elementos con plantilla en una CollectionView")](populate-data-images/datatemplate-large.png#lightbox "Elementos con plantilla en una CollectionView")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento del [`CollectionView`](xref:Xamarin.Forms.CollectionView) se puede elegir en tiempo de ejecución, en función del valor del elemento, estableciendo la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
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

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) está establecida en un objeto `MonkeyDataTemplateSelector`. En el ejemplo siguiente se muestra la clase `MonkeyDataTemplateSelector`:

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

La clase `MonkeyDataTemplateSelector` define `AmericanMonkey` y `OtherMonkey` propiedades de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se establecen en distintas plantillas de datos. La invalidación `OnSelectTemplate` devuelve la plantilla de `AmericanMonkey`, que muestra el nombre y la ubicación de Monkey en verde azulado, cuando el nombre de Monkey contiene "America". Cuando el nombre de Monkey no contiene "America", el reemplazo `OnSelectTemplate` devuelve la plantilla de `OtherMonkey`, que muestra el nombre y la ubicación de Monkey en Silver:

[![Captura de pantalla de selección de plantilla de elemento de tiempo de ejecución de CollectionView, en iOS y Android](populate-data-images/datatemplateselector.png "Selección de plantilla de elementos en tiempo de ejecución en una CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Selección de plantilla de elementos en tiempo de ejecución en una CollectionView")

Para obtener más información sobre los selectores de plantilla de datos, vea [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Al utilizar [`CollectionView`](xref:Xamarin.Forms.CollectionView), no establezca nunca el elemento raíz de los objetos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en un `ViewCell`. Esto hará que se produzca una excepción porque `CollectionView` no tiene ningún concepto de celdas.

## <a name="context-menus"></a>Menús contextuales

[`CollectionView`](xref:Xamarin.Forms.CollectionView) admite menús contextuales para elementos de datos a través de la `SwipeView`, que revela el menú contextual con un gesto de deslizar rápidamente. El `SwipeView` es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual para ese elemento de contenido. Por lo tanto, los menús contextuales se implementan para un `CollectionView` mediante la creación de una `SwipeView` que define el contenido que se ajusta a la `SwipeView` y los elementos de menú contextual que se revelan mediante el gesto de deslizar rápidamente. Esto se logra estableciendo el `SwipeView` como la vista raíz en el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define la apariencia de cada elemento de datos en el `CollectionView`:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <SwipeView>
                <SwipeView.LeftItems>
                    <SwipeItems>
                        <SwipeItem Text="Favorite"
                                   IconImageSource="favorite.png"
                                   BackgroundColor="LightGreen"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.FavoriteCommand}"
                                   CommandParameter="{Binding}" />
                        <SwipeItem Text="Delete"
                                   IconImageSource="delete.png"
                                   BackgroundColor="LightPink"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.DeleteCommand}"
                                   CommandParameter="{Binding}" />
                    </SwipeItems>
                </SwipeView.LeftItems>
                <Grid BackgroundColor="White"
                      Padding="10">
                    <!-- Define item appearance -->
                </Grid>
            </SwipeView>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    // Define item appearance
    Grid grid = new Grid { Padding = 10, BackgroundColor = Color.White };
    // ...

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: collectionView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: collectionView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.LeftItems = new SwipeItems { favoriteSwipeItem, deleteSwipeItem };
    swipeView.Content = grid;    
    return swipeView;
});
```

En este ejemplo, el contenido de la `SwipeView` es un [`Grid`](xref:Xamarin.Forms.Grid) que define la apariencia de cada elemento en la [`CollectionView`](xref:Xamarin.Forms.CollectionView). Los elementos de deslizamiento se utilizan para realizar acciones en el contenido de la `SwipeView` y se revelan cuando el control se desliza rápidamente desde el lado izquierdo:

[![Captura de pantalla de elementos de menú contextual de CollectionView, en iOS y Android](populate-data-images/swipeview.png "CollectionView con elementos de menú contextual de SwipeView")](populate-data-images/swipeview-large.png#lightbox "CollectionView con elementos de menú contextual de SwipeView")

`SwipeView` admite cuatro direcciones de deslizamiento diferentes, con la dirección de deslizamiento que se define en la colección de `SwipeItems` direccional a la que se agregan los objetos `SwipeItems`. De forma predeterminada, se ejecuta un dedo al puntear en el usuario. Además, una vez que se ha ejecutado un dedo, se ocultan los elementos de deslizamiento y se vuelve a mostrar el contenido de la `SwipeView`. Sin embargo, estos comportamientos se pueden cambiar.

Para obtener más información sobre el control de `SwipeView`, consulte [Xamarin. Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Extraer para actualizar

[`CollectionView`](xref:Xamarin.Forms.CollectionView) admite la funcionalidad de extracción para actualizar a través de la `RefreshView`, lo que permite que los datos que se muestran se actualicen en la lista de elementos. El `RefreshView` es un control contenedor que proporciona funcionalidad de extracción para actualizar a su elemento secundario, siempre que el elemento secundario admita contenido desplazable. Por lo tanto, la extracción de la actualización se implementa para un `CollectionView` estableciéndolo como el elemento secundario de un `RefreshView`:

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CollectionView ItemsSource="{Binding Animals}">
        ...
    </CollectionView>
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

CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = collectionView;
// ...
```

Cuando el usuario inicia una actualización, se ejecuta el `ICommand` definido por la propiedad `Command`, que debe actualizar los elementos que se muestran. Se muestra una visualización de actualización mientras se produce la actualización, que consta de un círculo de progreso animado:

[![Captura de pantalla de incorporación de cambios a la actualización de CollectionView en iOS y Android](populate-data-images/pull-to-refresh.png "Incorporación de cambios a la actualización de CollectionView")](populate-data-images/pull-to-refresh-large.png#lightbox "Incorporación de cambios a la actualización de CollectionView")

El valor de la propiedad `RefreshView.IsRefreshing` indica el estado actual del `RefreshView`. Cuando el usuario desencadena una actualización, esta propiedad pasará automáticamente a `true`. Una vez finalizada la actualización, debe restablecer la propiedad a `false`.

Para obtener más información sobre `RefreshView`, consulte [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Cargar datos incrementalmente

[`CollectionView`](xref:Xamarin.Forms.CollectionView) admite la carga de datos incrementalmente a medida que los usuarios se desplacen por los elementos. Esto permite escenarios como la carga asincrónica de una página de datos de un servicio Web, a medida que el usuario se desplaza. Además, el punto en el que se cargan más datos se puede configurar para que los usuarios no vean el espacio en blanco o se detengan del desplazamiento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades para controlar la carga incremental de datos:

- `RemainingItemsThreshold`, de tipo `int`, el umbral de elementos que todavía no están visibles en la lista en la que se desencadenará el evento `RemainingItemsThresholdReached`.
- `RemainingItemsThresholdReachedCommand`, de tipo `ICommand`, que se ejecuta cuando se alcanza la `RemainingItemsThreshold`.
- `RemainingItemsThresholdReachedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) también define un evento de `RemainingItemsThresholdReached` que se desencadena cuando el `CollectionView` se desplaza lo suficiente como para que no se muestren los elementos de `RemainingItemsThreshold`. Este evento se puede controlar para cargar más elementos. Además, cuando se desencadena el evento `RemainingItemsThresholdReached`, se ejecuta el `RemainingItemsThresholdReachedCommand`, lo que permite que la carga de datos incrementales se realice en un ViewModel.

El valor predeterminado de la propiedad `RemainingItemsThreshold` es-1, que indica que nunca se desencadenará el evento `RemainingItemsThresholdReached`. Cuando el valor de la propiedad es 0, el evento de `RemainingItemsThresholdReached` se activará cuando se muestre el último elemento del [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) . En el caso de valores mayores que 0, el evento `RemainingItemsThresholdReached` se desencadenará cuando el `ItemsSource` contenga ese número de elementos todavía no se haya desplazado a.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) valida la propiedad `RemainingItemsThreshold` de modo que su valor sea siempre mayor o igual que-1.

En el ejemplo de XAML siguiente se muestra un [`CollectionView`](xref:Xamarin.Forms.CollectionView) que carga datos incrementalmente:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

En este ejemplo de código, el evento `RemainingItemsThresholdReached` se desencadena cuando hay 5 elementos que todavía no se han desplazado a y, en respuesta, ejecuta el controlador de eventos `OnCollectionViewRemainingItemsThresholdReached`:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Los datos también se pueden cargar de forma incremental enlazando el `RemainingItemsThresholdReachedCommand` a una implementación de `ICommand` en el ViewModel.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [RefreshView de Xamarin. Forms](~/xamarin-forms/user-interface/refreshview.md)
- [SwipeView de Xamarin. Forms](~/xamarin-forms/user-interface/swipeview.md)
- [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creación de un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
