---
title: Datos de Xamarin. Forms CarouselView
description: Un CarouselView se rellena con datos estableciendo su propiedad ItemsSource en cualquier colección que implementa IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/17/2019
ms.openlocfilehash: 7d1183bf0c741b5a7ca02b43c4edb0c640ee1ac2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488234"
---
# <a name="xamarinforms-carouselview-data"></a>Datos de Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) incluye las siguientes propiedades que definen los datos que se van a mostrar y su apariencia:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de tipo `IEnumerable`, especifica la colección de elementos que se van a mostrar y tiene un valor predeterminado de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica la plantilla que se va a aplicar a cada elemento de la colección de elementos que se va a mostrar.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) define una propiedad `ItemsUpdatingScrollMode` que representa el comportamiento de desplazamiento de la `CarouselView` cuando se agregan nuevos elementos a ella. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Rellenar un CarouselView con datos

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se rellena con datos estableciendo su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) en cualquier colección que implementa `IEnumerable`. Los elementos se pueden agregar en XAML inicializando la propiedad `ItemsSource` de una matriz de cadenas:

```xaml
<CarouselView>
    <CarouselView.ItemsSource>
        <x:Array Type="{x:Type x:String}">
            <x:String>Baboon</x:String>
            <x:String>Capuchin Monkey</x:String>
            <x:String>Blue Monkey</x:String>
            <x:String>Squirrel Monkey</x:String>
            <x:String>Golden Lion Tamarin</x:String>
            <x:String>Howler Monkey</x:String>
            <x:String>Japanese Macaque</x:String>
        </x:Array>
    </CarouselView.ItemsSource>
</CarouselView>
```

> [!NOTE]
> Tenga en cuenta que el elemento `x:Array` requiere un atributo `Type` que indica el tipo de los elementos de la matriz.

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.ItemsSource = new string[]
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

> [!IMPORTANT]
> Si es necesario actualizar el [`CarouselView`](xref:Xamarin.Forms.CarouselView) a medida que se agregan, quitan o cambian elementos en la colección subyacente, la colección subyacente debe ser una colección `IEnumerable` que envíe notificaciones de cambios de propiedades, como `ObservableCollection`.

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) muestra los elementos horizontalmente. Las capturas de pantallas siguientes muestran un `CarouselView` que muestra distintos elementos de cadena en iOS y Android:

[![Captura de pantalla de CarouselView que contiene elementos de texto, en iOS y Android](populate-data-images/text.png "Elementos de texto en un CarouselView")](populate-data-images/text-large.png#lightbox "Elementos de texto en un CarouselView")

Para obtener información sobre cómo cambiar la orientación de [`CarouselView`](xref:Xamarin.Forms.CarouselView) , consulte [diseño de Xamarin. Forms CarouselView](layout.md). Para obtener información sobre cómo definir la apariencia de cada elemento en el `CarouselView`, consulte [definir la apariencia del elemento](#define-item-appearance).

### <a name="data-binding"></a>Enlace de datos

[`CarouselView`](xref:Xamarin.Forms.CarouselView) se pueden rellenar con datos mediante el enlace de datos para enlazar su propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a una colección `IEnumerable`. En XAML, esto se consigue con la extensión de marcado `Binding`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

En este ejemplo, los datos de la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) se enlazan a la propiedad `Monkeys` del ViewModel conectado.

> [!NOTE]
> Los enlaces compilados se pueden habilitar para mejorar el rendimiento del enlace de datos en aplicaciones de Xamarin. Forms. Para obtener más información, vea [Enlaces compilados](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento del [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede definir estableciendo la propiedad [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Los elementos especificados en la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento en el `CarouselView`. En el ejemplo, el diseño dentro del `DataTemplate` se administra mediante una [`StackLayout`](xref:Xamarin.Forms.StackLayout), y los datos se muestran con un objeto [`Image`](xref:Xamarin.Forms.Image) y tres objetos [`Label`](xref:Xamarin.Forms.Label) , que se enlazan a las propiedades de la clase `Monkey`:

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

La apariencia de cada elemento del [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede elegir en tiempo de ejecución, en función del valor del elemento, estableciendo la propiedad [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un objeto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

La clase `MonkeyDataTemplateSelector` define las propiedades `AmericanMonkey` y `OtherMonkey` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. La invalidación `OnSelectTemplate` devuelve la plantilla de `AmericanMonkey` cuando el nombre Monkey contiene "America". Cuando el nombre de Monkey no contiene "America", el reemplazo `OnSelectTemplate` devuelve la plantilla de `OtherMonkey`, que muestra los datos atenuados:

[![Captura de pantalla de selección de plantilla de elemento de tiempo de ejecución de CarouselView, en iOS y Android](populate-data-images/datatemplateselector.png "Selección de plantilla de elementos en tiempo de ejecución en un CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Selección de plantilla de elementos en tiempo de ejecución en un CarouselView")

Para obtener más información sobre los selectores de plantilla de datos, vea [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Al utilizar [`CarouselView`](xref:Xamarin.Forms.CarouselView), no establezca nunca el elemento raíz de los objetos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en un `ViewCell`. Esto hará que se produzca una excepción porque `CarouselView` no tiene ningún concepto de celdas.

## <a name="display-indicators"></a>Mostrar indicadores

Los indicadores, que representan el número de elementos y la posición actual en un `CarouselView`, se pueden mostrar junto al `CarouselView`. Esto puede realizarse con el control `IndicatorView`:

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

En este ejemplo, el `IndicatorView` se representa debajo del `CarouselView`, con un indicador para cada elemento de la `CarouselView`. El `IndicatorView` se rellena con datos estableciendo la propiedad `ItemsSourceBy` en el objeto `CarouselView`. Cada indicador es un círculo gris claro, mientras que el indicador que representa el elemento actual del `CarouselView` es gris oscuro:

[![Captura de pantalla de CarouselView y IndicatorView en iOS y Android](populate-data-images/indicators.png "Círculos IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos IndicatorView")

> [!IMPORTANT]
> Al establecer la propiedad `ItemsSourceBy`, se produce el enlace de la propiedad `IndicatorView.Position` a la propiedad `CarouselView.Position` y la propiedad `IndicatorView.ItemsSource` enlaza a la propiedad `CarouselView.ItemsSource`.

Para obtener más información sobre los indicadores, consulte [Xamarin. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md).

## <a name="pull-to-refresh"></a>Extraer para actualizar

[`CarouselView`](xref:Xamarin.Forms.CarouselView) admite la funcionalidad de extracción para actualizar a través de la `RefreshView`, lo que permite que los datos que se muestran se actualicen al desplazarse por los elementos. El `RefreshView` es un control contenedor que proporciona funcionalidad de extracción para actualizar a su elemento secundario, siempre que el elemento secundario admita contenido desplazable. Por lo tanto, la extracción de la actualización se implementa para un `CarouselView` estableciéndolo como el elemento secundario de un `RefreshView`:

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

Cuando el usuario inicia una actualización, se ejecuta el `ICommand` definido por la propiedad `Command`, que debe actualizar los elementos que se muestran. Se muestra una visualización de actualización mientras se produce la actualización, que consta de un círculo de progreso animado:

[![Captura de pantalla de incorporación de cambios a la actualización de CarouselView en iOS y Android](populate-data-images/pull-to-refresh.png "Incorporación de cambios a la actualización de CarouselView")](populate-data-images/pull-to-refresh-large.png#lightbox "Incorporación de cambios a la actualización de CarouselView")

El valor de la propiedad `RefreshView.IsRefreshing` indica el estado actual del `RefreshView`. Cuando el usuario desencadena una actualización, esta propiedad pasará automáticamente a `true`. Una vez finalizada la actualización, debe restablecer la propiedad a `false`.

Para obtener más información sobre `RefreshView`, consulte [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Cargar datos incrementalmente

[`CarouselView`](xref:Xamarin.Forms.CarouselView) admite la carga de datos incrementalmente a medida que los usuarios se desplacen por los elementos. Esto permite escenarios como la carga asincrónica de una página de datos de un servicio Web, a medida que el usuario se desplaza. Además, el punto en el que se cargan más datos se puede configurar para que los usuarios no vean el espacio en blanco o se detengan del desplazamiento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades para controlar la carga incremental de datos:

- `RemainingItemsThreshold`, de tipo `int`, el umbral de elementos que todavía no están visibles en la lista en la que se desencadenará el evento `RemainingItemsThresholdReached`.
- `RemainingItemsThresholdReachedCommand`, de tipo `ICommand`, que se ejecuta cuando se alcanza la `RemainingItemsThreshold`.
- `RemainingItemsThresholdReachedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también define un evento de `RemainingItemsThresholdReached` que se desencadena cuando el `CarouselView` se desplaza lo suficiente como para que no se muestren los elementos de `RemainingItemsThreshold`. Este evento se puede controlar para cargar más elementos. Además, cuando se desencadena el evento `RemainingItemsThresholdReached`, se ejecuta el `RemainingItemsThresholdReachedCommand`, lo que permite que la carga de datos incrementales se realice en un ViewModel.

El valor predeterminado de la propiedad `RemainingItemsThreshold` es-1, que indica que nunca se desencadenará el evento `RemainingItemsThresholdReached`. Cuando el valor de la propiedad es 0, el evento de `RemainingItemsThresholdReached` se activará cuando se muestre el último elemento del [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) . En el caso de valores mayores que 0, el evento `RemainingItemsThresholdReached` se desencadenará cuando el `ItemsSource` contenga ese número de elementos todavía no se haya desplazado a.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) valida la propiedad `RemainingItemsThreshold` de modo que su valor sea siempre mayor o igual que-1.

En el ejemplo de XAML siguiente se muestra un [`CarouselView`](xref:Xamarin.Forms.CarouselView) que carga datos incrementalmente:

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

En este ejemplo de código, el evento `RemainingItemsThresholdReached` se desencadena cuando hay dos elementos que todavía no se han desplazado a y, en respuesta, ejecuta el controlador de eventos `OnCollectionViewRemainingItemsThresholdReached`:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Los datos también se pueden cargar de forma incremental enlazando el `RemainingItemsThresholdReachedCommand` a una implementación de `ICommand` en el ViewModel.

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [IndicatorView de Xamarin. Forms](~/xamarin-forms/user-interface/indicatorview.md)
- [RefreshView de Xamarin. Forms](~/xamarin-forms/user-interface/refreshview.md)
- [Enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creación de un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
