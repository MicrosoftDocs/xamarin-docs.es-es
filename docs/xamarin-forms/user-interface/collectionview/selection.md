---
title: Xamarin.Forms Selección de CollectionView
description: De forma predeterminada, la selección de CollectionView está deshabilitada. Sin embargo, se puede habilitar la selección única y múltiple.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fd4ac89b60cdac8509e5c057c698ef79f7e6e969
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375348"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin.Forms Selección de CollectionView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que controlan la selección de elementos:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) , el modo de selección.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de tipo `object` , el elemento seleccionado en la lista. Esta propiedad tiene un modo de enlace predeterminado de `TwoWay` , y tiene un `null` valor cuando no se selecciona ningún elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de tipo `IList<object>` , los elementos seleccionados en la lista. Esta propiedad tiene un modo de enlace predeterminado de `OneWay` , y tiene un `null` valor cuando no se selecciona ningún elemento.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de tipo `ICommand` , que se ejecuta cuando cambia el elemento seleccionado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de tipo `object` , que es el parámetro que se pasa a `SelectionChangedCommand` .

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

De forma predeterminada, la [`CollectionView`](xref:Xamarin.Forms.CollectionView) selección está deshabilitada. Sin embargo, este comportamiento se puede cambiar estableciendo el [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valor de la propiedad en uno de los [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) miembros de enumeración:

- `None` : indica que no se pueden seleccionar elementos. Este es el valor predeterminado.
- `Single` : indica que se puede seleccionar un solo elemento, con el elemento seleccionado resaltado.
- `Multiple` : indica que se pueden seleccionar varios elementos, con los elementos seleccionados resaltados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento que se desencadena cuando [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) cambia la propiedad, ya sea porque el usuario selecciona un elemento de la lista o cuando una aplicación establece la propiedad. Además, este evento también se desencadena cuando cambia la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad. El [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) objeto que acompaña al `SelectionChanged` evento tiene dos propiedades, ambas de tipo `IReadOnlyList<object>` :

- `PreviousSelection` : la lista de elementos que se seleccionaron antes de que cambiara la selección.
- `CurrentSelection` : la lista de elementos que se seleccionan después de cambiar la selección.

Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un `UpdateSelectedItems` método que actualiza la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad con una lista de elementos seleccionados, y solo activa una notificación de cambio única.

## <a name="single-selection"></a>Selección única

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Single` , se puede seleccionar un solo elemento de [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Cuando se selecciona un elemento, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad se establecerá en el valor del elemento seleccionado. Cuando esta propiedad cambia, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) se ejecuta (con el valor de [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que se pasa a `ICommand` ) y se [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento.

En el siguiente ejemplo de XAML [`CollectionView`](xref:Xamarin.Forms.CollectionView) se muestra un que puede responder a una selección de un solo elemento:

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

En este ejemplo de código, el `OnCollectionViewSelectionChanged` controlador de eventos se ejecuta cuando se [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento, con el controlador de eventos que recupera el elemento seleccionado anteriormente y el elemento seleccionado actual:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> Los [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) cambios que se producen como resultado de la modificación de la propiedad pueden desencadenar el evento [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

En las siguientes capturas de pantallas se muestra la selección de un solo elemento en un [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Captura de pantalla de una lista de CollectionView vertical con una sola selección, en iOS y Android](selection-images/single-selection.png "Lista de CollectionView vertical con selección única")](selection-images/single-selection-large.png#lightbox "Lista de CollectionView vertical con selección única")

## <a name="multiple-selection"></a>Selección múltiple

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Multiple` , [`CollectionView`](xref:Xamarin.Forms.CollectionView) se pueden seleccionar varios elementos en. Cuando se seleccionan los elementos, la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad se establecerá en los elementos seleccionados. Cuando esta propiedad cambia, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) se ejecuta (con el valor de [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que se pasa a `ICommand` ) y se [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento.

En el siguiente ejemplo de XAML [`CollectionView`](xref:Xamarin.Forms.CollectionView) se muestra un que puede responder a la selección de varios elementos:

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

En este ejemplo de código, el `OnCollectionViewSelectionChanged` controlador de eventos se ejecuta cuando se [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento, con el controlador de eventos que recupera los elementos seleccionados anteriormente y los elementos seleccionados actualmente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> Los [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) cambios que se producen como resultado de la modificación de la propiedad pueden desencadenar el evento [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

En las siguientes capturas de pantallas se muestra la selección de varios elementos en un [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Captura de pantalla de una lista de CollectionView vertical con selección múltiple, en iOS y Android](selection-images/multiple-selection.png "Lista de CollectionView vertical con selección múltiple")](selection-images/multiple-selection-large.png#lightbox "Lista de CollectionView vertical con selección múltiple")

## <a name="single-pre-selection"></a>Selección previa única

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Single` , se puede preseleccionar un único elemento de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) propiedad mediante el establecimiento de la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad en el elemento. En el siguiente ejemplo de XAML se muestra un `CollectionView` que preselecciona un solo elemento:

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
> La [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad tiene un modo de enlace predeterminado de `TwoWay` .

Los [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) datos de la propiedad se enlazan a la `SelectedMonkey` propiedad del modelo de vista conectado, que es de tipo `Monkey` . De forma predeterminada, `TwoWay` se utiliza un enlace para que si el usuario cambia el elemento seleccionado, el valor de la `SelectedMonkey` propiedad se establecerá en el `Monkey` objeto seleccionado. La `SelectedMonkey` propiedad se define en la `MonkeysViewModel` clase y se establece en el cuarto elemento de la `Monkeys` colección:

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

Por lo tanto, cuando [`CollectionView`](xref:Xamarin.Forms.CollectionView) aparece, el cuarto elemento de la lista se selecciona previamente:

[![Captura de pantalla de una lista de CollectionView vertical con una sola selección previa, en iOS y Android](selection-images/single-pre-selection.png "Lista de CollectionView vertical con una sola selección previa")](selection-images/single-pre-selection-large.png#lightbox "Lista de CollectionView vertical con una sola selección previa")

## <a name="multiple-pre-selection"></a>Selección previa múltiple

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Multiple` , [`CollectionView`](xref:Xamarin.Forms.CollectionView) se pueden preseleccionar varios elementos en. En el ejemplo de XAML siguiente `CollectionView` se muestra un que habilitará la selección previa de varios elementos:

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
> La [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad tiene un modo de enlace predeterminado de `OneWay` .

Los [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) datos de la propiedad se enlazan a la `SelectedMonkeys` propiedad del modelo de vista conectado, que es de tipo `ObservableCollection<object>` . La `SelectedMonkeys` propiedad se define en la `MonkeysViewModel` clase y se establece en el segundo, cuarto y quinto elemento de la `Monkeys` colección:

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

Por lo tanto, cuando [`CollectionView`](xref:Xamarin.Forms.CollectionView) aparece, los elementos segundo, cuarto y quinto de la lista se seleccionan previamente:

[![Captura de pantalla de una lista de CollectionView vertical con varias selecciones previas, en iOS y Android](selection-images/multiple-pre-selection.png "Lista de CollectionView vertical con varias preselección")](selection-images/multiple-pre-selection-large.png#lightbox "Lista de CollectionView vertical con varias preselección")

## <a name="clear-selections"></a>Borrar selecciones

Las [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedades y se pueden borrar estableciéndolo, o los objetos a los que se enlazan, `null` en.

## <a name="change-selected-item-color"></a>Cambiar el color de los elementos seleccionados

[`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar un cambio visual en el elemento seleccionado en `CollectionView` . Un caso de uso común para esto `VisualState` es cambiar el color de fondo del elemento seleccionado, que se muestra en el siguiente ejemplo de XAML:

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
> El [`Style`](xref:Xamarin.Forms.Style) que contiene `Selected` `VisualState` debe tener un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de propiedad que sea el tipo del elemento raíz de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que se establece como el valor de la `ItemTemplate` propiedad.

En este ejemplo, el [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de la propiedad se establece en `Grid` porque el elemento raíz de [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) es un [`Grid`](xref:Xamarin.Forms.Grid) . `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) Especifica que cuando se selecciona un elemento de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) , el [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) del elemento se establecerá en `LightSkyBlue` :

[![Captura de pantalla de una lista de CollectionView vertical con un color de selección única personalizado, en iOS y Android](selection-images/single-selection-color.png "Lista de CollectionView vertical con un color de selección única personalizado")](selection-images/single-selection-color-large.png#lightbox "Lista de CollectionView vertical con un color de selección única personalizado")

Para obtener más información sobre los estados visuales, vea [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Deshabilitar selección

[`CollectionView`](xref:Xamarin.Forms.CollectionView) la selección está deshabilitada de forma predeterminada. Sin embargo, si `CollectionView` tiene habilitada la selección, se puede deshabilitar estableciendo la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad en `None` :

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

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `None` , los elementos de [`CollectionView`](xref:Xamarin.Forms.CollectionView) no se pueden seleccionar, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad permanecerá `null` y el [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento no se desencadenará.

> [!NOTE]
> Cuando se ha seleccionado un elemento y [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) se ha cambiado la propiedad de `Single` a `None` , la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad se establecerá en `null` y el [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento se desencadenará con una `CurrentSelection` propiedad vacía.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)