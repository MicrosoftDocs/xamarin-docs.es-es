---
title: Xamarin.FormsInteracción con CarouselView
description: Se puede tener acceso al elemento mostrado actualmente en un CarouselView a través de las propiedades CurrentItem y position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57c501c0f789ce448d8381cbbccb46666cf06305
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137415"
---
# <a name="xamarinforms-carouselview-interaction"></a>Xamarin.FormsInteracción con CarouselView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define las siguientes propiedades que controlan la interacción del usuario:

- `CurrentItem`, de tipo `object` , que se muestra en el elemento actual. Esta propiedad tiene un modo de enlace predeterminado de `TwoWay` , y tiene un `null` valor cuando no hay datos para mostrar.
- `CurrentItemChangedCommand`, de tipo `ICommand` , que se ejecuta cuando cambia el elemento actual.
- `CurrentItemChangedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `CurrentItemChangedCommand`.
- `IsBounceEnabled`, de tipo `bool` , que especifica si `CarouselView` se rebotará en un límite de contenido. El valor predeterminado es `true`.
- `IsSwipeEnabled`, de tipo `bool` , que determina si un gesto de deslizar rápidamente cambiará el elemento mostrado. El valor predeterminado es `true`.
- `Position`, de tipo `int` , el índice del elemento actual de la colección subyacente. Esta propiedad tiene un modo de enlace predeterminado de `TwoWay` , y tiene un valor 0 cuando no hay datos para mostrar.
- `PositionChangedCommand`, de tipo `ICommand` , que se ejecuta cuando cambia la posición.
- `PositionChangedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `PositionChangedCommand`.
- `VisibleViews`, de tipo `ObservableCollection<View>` , que es una propiedad de solo lectura que contiene los objetos de los elementos que están visibles actualmente.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define un `CurrentItemChanged` evento que se desencadena cuando `CurrentItem` cambia la propiedad, ya sea debido al desplazamiento por el usuario, o cuando una aplicación establece la propiedad. El `CurrentItemChangedEventArgs` objeto que acompaña al `CurrentItemChanged` evento tiene dos propiedades, ambas de tipo `object` :

- `PreviousItem`: el elemento anterior, después del cambio de la propiedad.
- `CurrentItem`: el elemento actual, después de cambiar la propiedad.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)también define un `PositionChanged` evento que se desencadena cuando `Position` cambia la propiedad, ya sea debido al desplazamiento por el usuario, o cuando una aplicación establece la propiedad. El `PositionChangedEventArgs` objeto que acompaña al `PositionChanged` evento tiene dos propiedades, ambas de tipo `int` :

- `PreviousPosition`: la posición anterior, después del cambio de la propiedad.
- `CurrentPosition`: la posición actual, después del cambio de la propiedad.

## <a name="respond-to-the-current-item-changing"></a>Responder al cambio del elemento actual

Cuando el elemento mostrado actualmente cambie, la `CurrentItem` propiedad se establecerá en el valor del elemento. Cuando esta propiedad cambia, `CurrentItemChangedCommand` se ejecuta con el valor de `CurrentItemChangedCommandParameter` que se pasa a `ICommand` . `Position`A continuación, la propiedad se actualiza y se `CurrentItemChanged` desencadena el evento.

> [!IMPORTANT]
> La `Position` propiedad cambia cuando `CurrentItem` cambia la propiedad. Esto hará `PositionChangedCommand` que se ejecute y se `PositionChanged` desencadene el evento.

### <a name="event"></a>Evento

En el ejemplo de XAML siguiente [`CarouselView`](xref:Xamarin.Forms.CarouselView) se muestra un objeto que utiliza un controlador de eventos para responder al cambio del elemento actual:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

En este ejemplo, el `OnCurrentItemChanged` controlador de eventos se ejecuta cuando se `CurrentItemChanged` activa el evento:

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

En este ejemplo, el `OnCurrentItemChanged` controlador de eventos expone los elementos anteriores y actuales:

[![Captura de pantalla de un CarouselView con elementos anteriores y actuales, en iOS y Android](interaction-images/current-item-events.png "CarouselView con elementos actuales y anteriores")](interaction-images/current-item-events-large.png#lightbox "CarouselView con elementos actuales y anteriores")

### <a name="command"></a>Get-Help

En el siguiente ejemplo de XAML [`CarouselView`](xref:Xamarin.Forms.CarouselView) se muestra un que usa un comando para responder al elemento actual Changing:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

En este ejemplo, la `CurrentItemChangedCommand` propiedad se enlaza a la `ItemChangedCommand` propiedad, pasando el `CurrentItem` valor de la propiedad como argumento. `ItemChangedCommand`Después, puede responder al cambio del elemento actual, según sea necesario:

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

En este ejemplo, `ItemChangedCommand` actualiza los objetos que almacenan los elementos anteriores y actuales.

## <a name="respond-to-the-position-changing"></a>Responder al cambio de posición

Cuando el elemento mostrado actualmente cambia, la `Position` propiedad se establecerá en el índice del elemento actual de la colección subyacente. Cuando esta propiedad cambia, `PositionChangedCommand` se ejecuta con el valor de `PositionChangedCommandParameter` que se pasa a `ICommand` . `PositionChanged`A continuación, se activa el evento. Si la `Position` propiedad se ha cambiado mediante programación, se [`CarouselView`](xref:Xamarin.Forms.CarouselView) desplazará al elemento que se corresponde con el `Position` valor.

> [!NOTE]
> Si el valor de la propiedad se establece `Position` en 0, se mostrará el primer elemento de la colección subyacente.

### <a name="event"></a>Evento

En el ejemplo de XAML siguiente [`CarouselView`](xref:Xamarin.Forms.CarouselView) se muestra un que usa un controlador de eventos para responder al `Position` cambio de propiedad:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

En este ejemplo, el `OnPositionChanged` controlador de eventos se ejecuta cuando se `PositionChanged` activa el evento:

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

En este ejemplo, el `OnCurrentItemChanged` controlador de eventos expone las posiciones anterior y actual:

[![Captura de pantalla de un CarouselView con las posiciones actuales y anteriores, en iOS y Android](interaction-images/current-position-events.png "CarouselView con las posiciones actual y anterior")](interaction-images/current-position-events-large.png#lightbox "CarouselView con las posiciones actual y anterior")

### <a name="command"></a>Get-Help

En el siguiente ejemplo de XAML [`CarouselView`](xref:Xamarin.Forms.CarouselView) se muestra un que usa un comando para responder al `Position` cambio de propiedad:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

En este ejemplo, la `PositionChangedCommand` propiedad se enlaza a la `PositionChangedCommand` propiedad, pasando el `Position` valor de la propiedad como argumento. `PositionChangedCommand`A continuación, puede responder a la posición que cambia, según sea necesario:

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

En este ejemplo, se `PositionChangedCommand` actualizan los objetos que almacenan las posiciones anterior y actual.

## <a name="preset-the-current-item"></a>Predefinir el elemento actual

El elemento actual de un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede establecer mediante programación estableciendo la `CurrentItem` propiedad en el elemento. En el ejemplo de XAML siguiente se muestra un `CarouselView` objeto que elige previamente el elemento actual:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> La `CurrentItem` propiedad tiene un modo de enlace predeterminado de `TwoWay` .

Los `CarouselView.CurrentItem` datos de la propiedad se enlazan a la `CurrentItem` propiedad del modelo de vista conectado, que es de tipo `Monkey` . De forma predeterminada, `TwoWay` se utiliza un enlace para que si el usuario cambia el elemento actual, el valor de la `CurrentItem` propiedad se establecerá en el `Monkey` objeto actual. La `CurrentItem` propiedad se define en la `MonkeysViewModel` clase:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

En este ejemplo, la `CurrentItem` propiedad se establece en el cuarto elemento de la `Monkeys` colección:

[![Captura de pantalla de un CarouselView con el elemento preestablecido, en iOS y Android](interaction-images/preset-item.png "CarouselView con elemento preestablecido")](interaction-images/preset-item-large.png#lightbox "CarouselView con elemento preestablecido")

## <a name="preset-the-position"></a>Valor preestablecido de la posición

El elemento mostrado [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede establecer mediante programación estableciendo la `Position` propiedad en el índice del elemento de la colección subyacente. En el ejemplo de XAML siguiente se muestra un `CarouselView` objeto que establece el elemento mostrado:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> La `Position` propiedad tiene un modo de enlace predeterminado de `TwoWay` .

Los `CarouselView.Position` datos de la propiedad se enlazan a la `Position` propiedad del modelo de vista conectado, que es de tipo `int` . De forma predeterminada, `TwoWay` se usa un enlace para que si el usuario se desplaza por el [`CarouselView`](xref:Xamarin.Forms.CarouselView) , el valor de la `Position` propiedad se establecerá en el índice del elemento mostrado. La `Position` propiedad se define en la `MonkeysViewModel` clase:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

En este ejemplo, la `Position` propiedad se establece en el cuarto elemento de la `Monkeys` colección:

[![Captura de pantalla de un CarouselView con la posición preestablecida, en iOS y Android](interaction-images/preset-position.png "CarouselView con posición preestablecida")](interaction-images/preset-position-large.png#lightbox "CarouselView con posición preestablecida")

## <a name="define-visual-states"></a>Definir Estados visuales

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define cuatro Estados visuales:

- `CurrentItem`representa el estado visual del elemento que se muestra actualmente.
- `PreviousItem`representa el estado visual del elemento mostrado previamente.
- `NextItem`representa el estado visual del elemento siguiente.
- `DefaultItem`representa el estado visual para el resto de los elementos.

Estos Estados visuales se pueden usar para iniciar cambios visuales en los elementos mostrados por [`CarouselView`](xref:Xamarin.Forms.CarouselView) .

En el siguiente ejemplo de XAML se muestra cómo definir los `CurrentItem` Estados de, `PreviousItem` , `NextItem` y `DefaultItem` :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="CurrentItem">
                            <VisualState.Setters>
                                <Setter Property="Scale"
                                        Value="1.1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="PreviousItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="NextItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="DefaultItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.25" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <!-- Item template content -->
                <Frame HasShadow="true">
                    ...
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

En este ejemplo, el `CurrentItem` estado visual especifica que el elemento actual mostrado por [`CarouselView`](xref:Xamarin.Forms.CarouselView) tendrá su [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad cambiada de su valor predeterminado de 1 a 1,1. Los `PreviousItem` `NextItem` Estados visual y especifican que los elementos que rodean al elemento actual se mostrarán con un [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valor de 0,5. El `DefaultItem` estado visual especifica que el resto de los elementos mostrados por se `CarouselView` mostrarán con un `Opacity` valor de 0,25.

> [!NOTE]
> Como alternativa, los Estados visuales se pueden definir en un [`Style`](xref:Xamarin.Forms.Style) que tiene un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de propiedad que es el tipo del elemento raíz de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que se establece como el valor de la `ItemTemplate` propiedad.

Las capturas de pantallas siguientes muestran los `CurrentItem` `PreviousItem` `NextItem` Estados visuales, y:

[![Captura de pantalla de un CarouselView con Estados visuales, en iOS y Android](interaction-images/visual-states.png "Estados visuales de CarouselView")](interaction-images/visual-states-large.png#lightbox "Estados visuales de CarouselView")

Para obtener más información sobre los estados visuales, vea [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Borrar el elemento actual

La `CurrentItem` propiedad se puede borrar estableciéndolo, o el objeto al que se enlaza, en `null` .

## <a name="disable-bounce"></a>Deshabilitar rebote

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rebota los elementos en los límites de contenido. Esto se puede deshabilitar estableciendo la `IsBounceEnabled` propiedad en `false` .

## <a name="disable-swipe-interaction"></a>Deshabilitar la interacción de deslizar rápidamente

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) permite a los usuarios desplazarse por los elementos con un gesto de deslizar rápidamente. Esta interacción de deslizar rápidamente puede deshabilitarse estableciendo la `IsSwipeEnabled` propiedad en `false` .

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Administrador de estado visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
