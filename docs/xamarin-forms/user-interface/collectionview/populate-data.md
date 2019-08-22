---
title: Datos de CollectionView de Xamarin. Forms
description: Un CollectionView se rellena con datos estableciendo su propiedad ItemsSource en cualquier colección que implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 6942baed6af2a2e9b2c713a8fe08cf4c8ed4416b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888545"
---
# <a name="xamarinforms-collectionview-data"></a>Datos de CollectionView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define las siguientes propiedades que definen los datos que se van a mostrar y su apariencia:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de tipo `IEnumerable`, especifica la colección de elementos que se van a mostrar y tiene un valor predeterminado `null`de.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica la plantilla que se va a aplicar a cada elemento de la colección de elementos que se va a mostrar.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)define una `ItemsUpdatingScrollMode` propiedad que representa el comportamiento `CollectionView` de desplazamiento de cuando se agregan nuevos elementos a él. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos](#load-data-incrementally)de forma incremental.

## <a name="populate-a-collectionview-with-data"></a>Rellenar un CollectionView con datos

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad en `IEnumerable`cualquier colección que implementa. Los elementos se pueden agregar en XAML inicializando `ItemsSource` la propiedad desde una matriz de cadenas:

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

El código de C# equivalente es:

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

> [!IMPORTANT]
> Si es necesario actualizar a medida que se agregan, quitan o cambian elementos en la colección subyacente, la colección subyacente debe ser `IEnumerable` una colección que envíe `ObservableCollection`notificaciones de cambios de propiedades, como. [`CollectionView`](xref:Xamarin.Forms.CollectionView)

De forma predeterminada [`CollectionView`](xref:Xamarin.Forms.CollectionView) , muestra los elementos en una lista vertical, tal como se muestra en las siguientes capturas de pantallas:

[ ![Captura de pantalla de la colección CollectionView que contiene elementos de texto, en iOS y](populate-data-images/text.png "elementos de texto Android en una colección CollectionView") ] (populate-data-images/text-large.png#lightbox "Elementos de texto de una CollectionView")

Para obtener información sobre cómo cambiar el [`CollectionView`](xref:Xamarin.Forms.CollectionView) diseño, vea [especificar un diseño](layout.md). Para obtener información sobre cómo definir la apariencia de cada elemento en `CollectionView`, vea definir la apariencia del [elemento](#define-item-appearance).

### <a name="data-binding"></a>Enlace de datos

[`CollectionView`](xref:Xamarin.Forms.CollectionView)se puede rellenar con datos mediante el enlace de datos [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) para enlazar `IEnumerable` su propiedad a una colección. En XAML, esto se consigue con la `Binding` extensión de marcado:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

En este ejemplo, los [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) datos de la propiedad se enlazan a la `Monkeys` propiedad del ViewModel conectado.

> [!NOTE]
> Los enlaces compilados se pueden habilitar para mejorar el rendimiento del enlace de datos en aplicaciones de Xamarin. Forms. Para obtener más información, vea [Enlaces compilados](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView) puede definirse estableciendo la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

El código de C# equivalente es:

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

Los elementos especificados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento de la lista. En el ejemplo, el `DataTemplate` diseño dentro de se administra mediante un. [`Grid`](xref:Xamarin.Forms.Grid) Contiene un [`Image`](xref:Xamarin.Forms.Image) objeto y dos [`Label`](xref:Xamarin.Forms.Label) objetos, que se enlazan a las propiedades de la `Monkey` clase: `Grid`

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

[ ![Captura de pantalla de CollectionView donde cada elemento tiene una plantilla, en iOS y](populate-data-images/datatemplate.png "elementos con plantilla de Android en una CollectionView") ] (populate-data-images/datatemplate-large.png#lightbox "Elementos con plantilla en una CollectionView")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento en [`CollectionView`](xref:Xamarin.Forms.CollectionView) se puede elegir en tiempo de ejecución, según el valor del elemento, estableciendo la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objeto:

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

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad se establece en un `MonkeyDataTemplateSelector` objeto. En el ejemplo siguiente se `MonkeyDataTemplateSelector` muestra la clase:

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

La clase `MonkeyDataTemplateSelector` define las propiedades `AmericanMonkey` y `OtherMonkey` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. La `OnSelectTemplate` invalidación devuelve `AmericanMonkey` la plantilla, que muestra el nombre y la ubicación de Monkey en verde azulado, cuando el nombre de Monkey contiene "America". Cuando el nombre de Monkey no contiene "America", `OnSelectTemplate` la invalidación `OtherMonkey` devuelve la plantilla, que muestra el nombre y la ubicación de Monkey en Silver:

[ ![Captura de pantalla de selección de plantilla de elemento de tiempo de ejecución de CollectionView, en iOS y Android](populate-data-images/datatemplateselector.png "selección de plantilla de elementos en tiempo de ejecución en una CollectionView") ] (populate-data-images/datatemplateselector-large.png#lightbox "Selección de plantilla de elementos en tiempo de ejecución en una CollectionView")

Para obtener más información sobre los selectores de plantilla de datos, vea [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Al utilizar [`CollectionView`](xref:Xamarin.Forms.CollectionView), no establezca nunca el elemento raíz de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) los objetos en `ViewCell`un. Esto hará que se produzca una excepción porque `CollectionView` no tiene concepto de celdas.

## <a name="load-data-incrementally"></a>Cargar datos incrementalmente

[`CollectionView`](xref:Xamarin.Forms.CollectionView)permite cargar datos de forma incremental a medida que los usuarios se desplazan por los elementos. Esto permite escenarios como la carga asincrónica de una página de datos de un servicio Web, a medida que el usuario se desplaza. Además, el punto en el que se cargan más datos se puede configurar para que los usuarios no vean el espacio en blanco o se detengan del desplazamiento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define las siguientes propiedades para controlar la carga incremental de datos:

- `RemainingItemsThreshold`, de tipo `int`, el umbral de elementos que todavía no están visibles en la lista en `RemainingItemsThresholdReached` la que se desencadenará el evento.
- `RemainingItemsThresholdReachedCommand`, de tipo `ICommand`, que se ejecuta cuando se `RemainingItemsThreshold` alcanza el.
- `RemainingItemsThresholdReachedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)también define un `RemainingItemsThresholdReached` evento que se desencadena `CollectionView` cuando se desplaza lo suficiente como para que `RemainingItemsThreshold` no se muestren los elementos. Este evento se puede controlar para cargar más elementos. Además, cuando se desencadena `RemainingItemsThresholdReached` el evento, se ejecuta `RemainingItemsThresholdReachedCommand` , lo que permite que la carga de datos incrementales se realice en un ViewModel.

El valor predeterminado de la `RemainingItemsThreshold` propiedad es-1, lo que indica que `RemainingItemsThresholdReached` el evento nunca se desencadenará. Cuando el valor de la propiedad es 0 `RemainingItemsThresholdReached` , el evento se desencadena cuando [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) se muestra el último elemento de. En el caso de valores mayores que `RemainingItemsThresholdReached` 0, el evento se desencadena `ItemsSource` cuando contiene ese número de elementos a los que todavía no se ha desplazado.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)valida la `RemainingItemsThreshold` propiedad de modo que su valor sea siempre mayor o igual que-1.

En el siguiente ejemplo de XAML [`CollectionView`](xref:Xamarin.Forms.CollectionView) se muestra un que carga datos incrementalmente:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

En este ejemplo de código, `RemainingItemsThresholdReached` el evento se desencadena cuando hay 5 elementos que todavía no se han desplazado a y, `OnCollectionViewRemainingItemsThresholdReached` en respuesta, ejecuta el controlador de eventos:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Los datos también se pueden cargar incrementalmente enlazando el `RemainingItemsThresholdReachedCommand` a una `ICommand` implementación de ViewModel.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Enlace de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creación de un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
