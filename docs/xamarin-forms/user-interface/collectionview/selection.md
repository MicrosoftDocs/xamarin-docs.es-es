---
title: Selección de CollectionView de Xamarin. Forms
description: De forma predeterminada, la selección de CollectionView está deshabilitada. Sin embargo, se puede habilitar la selección única y múltiple.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f1a3e8bb8959588e64339f70268370440f356be9
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738970"
---
# <a name="xamarinforms-collectionview-selection"></a>Selección de CollectionView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define las siguientes propiedades que controlan la selección de elementos:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), el modo de selección.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de tipo `object`, el elemento seleccionado en la lista. Esta propiedad tiene un modo de enlace predeterminado `TwoWay`de, y tiene `null` un valor cuando no se selecciona ningún elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de tipo `IList<object>`, los elementos seleccionados en la lista. Esta propiedad tiene un modo de enlace predeterminado `OneWay`de, y tiene `null` un valor cuando no se selecciona ningún elemento.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de tipo `ICommand`, que se ejecuta cuando cambia el elemento seleccionado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de tipo `object`, que es el parámetro que se pasa `SelectionChangedCommand`a.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

De forma predeterminada [`CollectionView`](xref:Xamarin.Forms.CollectionView) , la selección está deshabilitada. Sin embargo, este comportamiento se puede cambiar estableciendo el [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valor de la propiedad en uno [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) de los miembros de enumeración:

- `None`: indica que no se pueden seleccionar elementos. Este es el valor predeterminado.
- `Single`: indica que se puede seleccionar un solo elemento, con el elemento seleccionado resaltado.
- `Multiple`: indica que se pueden seleccionar varios elementos, con los elementos seleccionados resaltados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define un [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento que se desencadena cuando cambia [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) la propiedad, ya sea porque el usuario selecciona un elemento de la lista o cuando una aplicación establece la propiedad. Además, este evento también se desencadena cuando cambia la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad. El [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) objeto que acompaña al `SelectionChanged` evento tiene dos propiedades, ambas de tipo `IReadOnlyList<object>`:

- `PreviousSelection`: la lista de elementos que se seleccionaron antes de que cambiara la selección.
- `CurrentSelection`: la lista de elementos que se seleccionan después de cambiar la selección.

## <a name="single-selection"></a>Selección única

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Single`, se [`CollectionView`](xref:Xamarin.Forms.CollectionView) puede seleccionar un solo elemento de. Cuando se selecciona un elemento, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad se establecerá en el valor del elemento seleccionado. Cuando [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) esta propiedad cambia, se ejecuta (con el valor [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) de que se pasa a `ICommand`) y se desencadena el [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento.

En el siguiente ejemplo de XAML [`CollectionView`](xref:Xamarin.Forms.CollectionView) se muestra un que puede responder a una selección de un solo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

En este ejemplo de código, `OnCollectionViewSelectionChanged` el controlador de eventos se ejecuta [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) cuando se desencadena el evento, con el controlador de eventos que recupera el elemento seleccionado anteriormente y el elemento seleccionado actual:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> Los [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) cambios que se producen como resultado de la modificación de la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad pueden desencadenar el evento.

En las siguientes capturas de pantallas se muestra [`CollectionView`](xref:Xamarin.Forms.CollectionView)la selección de un solo elemento en un:

[ ![Captura de pantalla de una lista de CollectionView vertical con una sola selección, en la lista de iOS y Android de una]sola(selection-images/single-selection.png "selección") ] (selection-images/single-selection-large.png#lightbox "Lista de CollectionView vertical con selección única")

## <a name="multiple-selection"></a>Selección múltiple

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Multiple` [`CollectionView`](xref:Xamarin.Forms.CollectionView) , se pueden seleccionar varios elementos en. Cuando se seleccionan los elementos [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) , la propiedad se establecerá en los elementos seleccionados. Cuando [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) esta propiedad cambia, se ejecuta (con el valor [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) de que se pasa a `ICommand`) y se desencadena el [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento.

En el siguiente ejemplo de XAML [`CollectionView`](xref:Xamarin.Forms.CollectionView) se muestra un que puede responder a la selección de varios elementos:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

En este ejemplo de código, `OnCollectionViewSelectionChanged` el controlador de eventos se ejecuta [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) cuando se desencadena el evento, con el controlador de eventos que recupera los elementos seleccionados anteriormente y los elementos seleccionados actualmente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> Los [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) cambios que se producen como resultado de la modificación de la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad pueden desencadenar el evento.

En las siguientes capturas de pantallas se muestra [`CollectionView`](xref:Xamarin.Forms.CollectionView)la selección de varios elementos en un:

[ ![Captura de pantalla de una lista de CollectionView vertical con selección múltiple, en iOS y Android,](selection-images/multiple-selection.png "lista vertical de CollectionView con selección múltiple") ] (selection-images/multiple-selection-large.png#lightbox "Lista de CollectionView vertical con selección múltiple")

## <a name="single-pre-selection"></a>Selección previa única

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Single`, se puede preseleccionar un [`CollectionView`](xref:Xamarin.Forms.CollectionView) único elemento de la propiedad mediante el establecimiento [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) de la propiedad en el elemento. En el siguiente ejemplo de XAML `CollectionView` se muestra un que preselecciona un solo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> La [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad tiene un modo de enlace predeterminado `TwoWay`de.

El [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) datos de la propiedad se enlaza a la `SelectedMonkey` propiedad del modelo de vista conectada, que es de tipo `Monkey`. De forma predeterminada, `TwoWay` se utiliza un enlace para que si el usuario cambia el elemento seleccionado, el valor de `SelectedMonkey` la propiedad se establecerá en el `Monkey` objeto seleccionado. La `SelectedMonkey` propiedad se define en la `MonkeysViewModel` clase y se establece en el cuarto elemento de la `Monkeys` Colección:

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

Por lo tanto, [`CollectionView`](xref:Xamarin.Forms.CollectionView) cuando aparece, el cuarto elemento de la lista se selecciona previamente:

[ ![Captura de pantalla de una lista de CollectionView vertical con una sola selección previa, en iOS y Android,](selection-images/single-pre-selection.png "lista vertical de CollectionView con una sola selección previa") ] (selection-images/single-pre-selection-large.png#lightbox "Lista de CollectionView vertical con una sola selección previa")

## <a name="multiple-pre-selection"></a>Selección previa múltiple

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Multiple` [`CollectionView`](xref:Xamarin.Forms.CollectionView) , se pueden preseleccionar varios elementos en. En el ejemplo de XAML siguiente `CollectionView` se muestra un que habilitará la selección previa de varios elementos:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> La [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad tiene un modo de enlace predeterminado `OneWay`de.

El [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) datos de la propiedad se enlaza a la `SelectedMonkeys` propiedad del modelo de vista conectada, que es de tipo `ObservableCollection<object>`. La `SelectedMonkeys` propiedad se define en la `MonkeysViewModel` clase y se establece en el segundo, cuarto y quinto elemento de la `Monkeys` Colección:

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

Por lo tanto, [`CollectionView`](xref:Xamarin.Forms.CollectionView) cuando aparece, los elementos segundo, cuarto y quinto de la lista se seleccionan previamente:

[ ![Captura de pantalla de una lista de CollectionView vertical con varias selecciones preseleccionadas, en iOS y Android,](selection-images/multiple-pre-selection.png "lista vertical de CollectionView con varias preselección") ] (selection-images/multiple-pre-selection-large.png#lightbox "Lista de CollectionView vertical con varias preselección")

## <a name="clearing-selections"></a>Borrar selecciones

Las [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedades [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) y se pueden borrar estableciéndolo, o los objetos a los que se enlazan, `null`en.

## <a name="change-selected-item-color"></a>Cambiar el color de los elementos seleccionados

[`CollectionView`](xref:Xamarin.Forms.CollectionView)tiene un `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar un cambio visual en `CollectionView`el elemento seleccionado en. Un caso de uso común para `VisualState` esto es cambiar el color de fondo del elemento seleccionado, que se muestra en el siguiente ejemplo de XAML:

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
> El [`Style`](xref:Xamarin.Forms.Style) que `ItemTemplate` [contienedebetener`DataTemplate`](xref:Xamarin.Forms.DataTemplate)un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de propiedad que sea el tipo del elemento raíz de, que se establece como el valor de la propiedad. `Selected` `VisualState`

En este ejemplo, el [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de la propiedad se `Grid` establece en porque [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) el elemento raíz de es [`Grid`](xref:Xamarin.Forms.Grid)un. `LightSkyBlue` [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`CollectionView`](xref:Xamarin.Forms.CollectionView) Especifica que cuando se selecciona un elemento de la, el del elemento se establecerá en: `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState)

[ ![Captura de pantalla de una lista de CollectionView vertical con un color de selección única personalizado, en la lista de iOS y Android en](selection-images/single-selection-color.png "la lista vertical con un color de selección única personalizado") ] (selection-images/single-selection-color-large.png#lightbox "Lista de CollectionView vertical con un color de selección única personalizado")

Para obtener más información sobre los Estados visuales, consulte [Visual State Manager de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Deshabilitar selección

[`CollectionView`](xref:Xamarin.Forms.CollectionView)la selección está deshabilitada de forma predeterminada. Sin embargo, si `CollectionView` tiene habilitada la selección, se puede deshabilitar estableciendo [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) la propiedad `None`en:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Cuando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `None`, los elementos de [`CollectionView`](xref:Xamarin.Forms.CollectionView) no se pueden seleccionar, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) la propiedad permanecerá `null`y el [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento no se desencadenará.

> [!NOTE]
> Cuando se ha seleccionado un elemento y se [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ha cambiado la propiedad `Single` de `None`a, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) la propiedad se establecerá `null` en y [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) el evento se desencadenará con una `CurrentSelection` propiedad vacía. .

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
