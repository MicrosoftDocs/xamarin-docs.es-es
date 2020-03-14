---
title: Selección de CollectionView de Xamarin. Forms
description: De forma predeterminada, la selección de CollectionView está deshabilitada. Sin embargo, se puede habilitar la selección única y múltiple.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a4cc237ef738edeccf66f1a91a010e4831c1c72f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305916"
---
# <a name="xamarinforms-collectionview-selection"></a>Selección de CollectionView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que controlan la selección de elementos:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), el modo de selección.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de tipo `object`, el elemento seleccionado en la lista. Esta propiedad tiene un modo de enlace predeterminado de `TwoWay`y tiene un valor `null` cuando no se selecciona ningún elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de tipo `IList<object>`, los elementos seleccionados en la lista. Esta propiedad tiene un modo de enlace predeterminado de `OneWay`y tiene un valor `null` cuando no se selecciona ningún elemento.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de tipo `ICommand`, que se ejecuta cuando cambia el elemento seleccionado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de tipo `object`, que es el parámetro que se pasa a la `SelectionChangedCommand`.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) selección está deshabilitada. Sin embargo, este comportamiento se puede cambiar estableciendo el valor de la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) en uno de los miembros de enumeración [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) :

- `None`: indica que no se pueden seleccionar elementos. Este es el valor predeterminado.
- `Single`: indica que se puede seleccionar un solo elemento, con el elemento seleccionado resaltado.
- `Multiple`: indica que se pueden seleccionar varios elementos, con los elementos seleccionados resaltados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) que se desencadena cuando cambia la propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) , ya sea porque el usuario selecciona un elemento de la lista o cuando una aplicación establece la propiedad. Además, este evento también se desencadena cuando cambia la propiedad [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) . El objeto [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) que acompaña al evento `SelectionChanged` tiene dos propiedades, ambas de tipo `IReadOnlyList<object>`:

- `PreviousSelection`: la lista de elementos que se seleccionaron antes de que cambiara la selección.
- `CurrentSelection`: la lista de elementos seleccionados, después de cambiar la selección.

Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un método de `UpdateSelectedItems` que actualiza la propiedad [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) con una lista de elementos seleccionados, y solo activa una notificación de cambio única.

## <a name="single-selection"></a>Selección única

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) está establecida en `Single`, se puede seleccionar un único elemento de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Cuando se selecciona un elemento, la propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) se establecerá en el valor del elemento seleccionado. Cuando esta propiedad cambia, se ejecuta el [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) (con el valor de la [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que se pasa al `ICommand`) y se activa el evento de [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

En el ejemplo de XAML siguiente se muestra un [`CollectionView`](xref:Xamarin.Forms.CollectionView) que puede responder a la selección de un solo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

En este ejemplo de código, el controlador de eventos `OnCollectionViewSelectionChanged` se ejecuta cuando se activa el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) , con el controlador de eventos que recupera el elemento seleccionado anteriormente y el elemento seleccionado actualmente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> Los cambios que se producen como resultado del cambio de la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) pueden desencadenar el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

Las siguientes capturas de pantallas muestran una selección de un solo elemento en un [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Captura de pantalla de una lista de CollectionView vertical con una sola selección, en iOS y Android](selection-images/single-selection.png "Lista de CollectionView vertical con selección única")](selection-images/single-selection-large.png#lightbox "Lista de CollectionView vertical con selección única")

## <a name="multiple-selection"></a>Selección múltiple

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) está establecida en `Multiple`, se pueden seleccionar varios elementos de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Cuando se seleccionan los elementos, la propiedad [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) se establecerá en los elementos seleccionados. Cuando esta propiedad cambia, se ejecuta el [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) (con el valor de la [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que se pasa al `ICommand`) y se activa el evento de [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

En el ejemplo de XAML siguiente se muestra un [`CollectionView`](xref:Xamarin.Forms.CollectionView) que puede responder a la selección de varios elementos:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

En este ejemplo de código, el controlador de eventos `OnCollectionViewSelectionChanged` se ejecuta cuando se activa el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) , con el controlador de eventos que recupera los elementos seleccionados anteriormente y los elementos seleccionados actualmente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> Los cambios que se producen como resultado del cambio de la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) pueden desencadenar el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

En las siguientes capturas de pantallas se muestra la selección de varios elementos en un [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Captura de pantalla de una lista de CollectionView vertical con selección múltiple, en iOS y Android](selection-images/multiple-selection.png "Lista de CollectionView vertical con selección múltiple")](selection-images/multiple-selection-large.png#lightbox "Lista de CollectionView vertical con selección múltiple")

## <a name="single-pre-selection"></a>Selección previa única

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) está establecida en `Single`, se puede preseleccionar un solo elemento de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) estableciendo la propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) en el elemento. En el ejemplo de XAML siguiente se muestra un `CollectionView` que preselecciona un solo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> La propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) tiene un modo de enlace predeterminado de `TwoWay`.

Los datos de la propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) se enlazan a la propiedad `SelectedMonkey` del modelo de vista conectado, que es de tipo `Monkey`. De forma predeterminada, se usa un enlace `TwoWay` de modo que, si el usuario cambia el elemento seleccionado, el valor de la propiedad `SelectedMonkey` se establecerá en el objeto `Monkey` seleccionado. La propiedad `SelectedMonkey` se define en la clase `MonkeysViewModel` y está establecida en el cuarto elemento de la colección `Monkeys`:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

Por lo tanto, cuando aparezca el [`CollectionView`](xref:Xamarin.Forms.CollectionView) , el cuarto elemento de la lista se seleccionará previamente:

[![Captura de pantalla de una lista de CollectionView vertical con una sola selección previa, en iOS y Android](selection-images/single-pre-selection.png "Lista de CollectionView vertical con una sola selección previa")](selection-images/single-pre-selection-large.png#lightbox "Lista de CollectionView vertical con una sola selección previa")

## <a name="multiple-pre-selection"></a>Selección previa múltiple

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) está establecida en `Multiple`, se pueden preseleccionar varios elementos de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) . En el ejemplo de XAML siguiente se muestra un `CollectionView` que habilitará la selección previa de varios elementos:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> La propiedad [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) tiene un modo de enlace predeterminado de `OneWay`.

Los datos de la propiedad [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) se enlazan a la propiedad `SelectedMonkeys` del modelo de vista conectado, que es de tipo `ObservableCollection<object>`. La propiedad `SelectedMonkeys` se define en la clase `MonkeysViewModel` y está establecida en los elementos segundo, cuarto y quinto de la colección `Monkeys`:

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

Por lo tanto, cuando aparezca el [`CollectionView`](xref:Xamarin.Forms.CollectionView) , el segundo, cuarto y quinto elemento de la lista se seleccionan previamente:

[![Captura de pantalla de una lista de CollectionView vertical con varias selecciones previas, en iOS y Android](selection-images/multiple-pre-selection.png "Lista de CollectionView vertical con varias preselección")](selection-images/multiple-pre-selection-large.png#lightbox "Lista de CollectionView vertical con varias preselección")

## <a name="clear-selections"></a>Borrar selecciones

Las propiedades [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) y [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) se pueden borrar estableciéndolo, o los objetos a los que se enlazan, en `null`.

## <a name="change-selected-item-color"></a>Cambiar el color de los elementos seleccionados

[`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un [`VisualState`](xref:Xamarin.Forms.VisualState) de `Selected` que se puede utilizar para iniciar un cambio visual en el elemento seleccionado en el `CollectionView`. Un caso de uso común para este `VisualState` es cambiar el color de fondo del elemento seleccionado, que se muestra en el siguiente ejemplo de XAML:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> El [`Style`](xref:Xamarin.Forms.Style) que contiene el `VisualState` de `Selected` debe tener un valor de propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) que sea el tipo del elemento raíz de la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que se establece como el valor de la propiedad `ItemTemplate`.

En este ejemplo, el valor de la propiedad [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) se establece en `Grid` porque el elemento raíz de la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) es un [`Grid`](xref:Xamarin.Forms.Grid). El [`VisualState`](xref:Xamarin.Forms.VisualState) `Selected` especifica que cuando se selecciona un elemento del [`CollectionView`](xref:Xamarin.Forms.CollectionView) , el [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) del elemento se establecerá en `LightSkyBlue`:

[![Captura de pantalla de una lista de CollectionView vertical con un color de selección única personalizado, en iOS y Android](selection-images/single-selection-color.png "Lista de CollectionView vertical con un color de selección única personalizado")](selection-images/single-selection-color-large.png#lightbox "Lista de CollectionView vertical con un color de selección única personalizado")

Para obtener más información sobre los Estados visuales, consulte [Visual State Manager de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Deshabilitar selección

[`CollectionView`](xref:Xamarin.Forms.CollectionView) selección está deshabilitada de forma predeterminada. Sin embargo, si un `CollectionView` tiene habilitada la selección, se puede deshabilitar estableciendo la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) en `None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Cuando la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) está establecida en `None`, los elementos de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) no se pueden seleccionar, la propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) permanecerá `null`y no se desencadenará el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

> [!NOTE]
> Cuando se ha seleccionado un elemento y se cambia la propiedad [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) de `Single` a `None`, la propiedad [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) se establecerá en `null` y el evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se desencadenará con una propiedad `CurrentSelection` vacía.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
