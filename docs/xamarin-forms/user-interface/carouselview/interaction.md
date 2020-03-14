---
title: Interacción de Xamarin. Forms CarouselView
description: Se puede tener acceso al elemento mostrado actualmente en un CarouselView a través de las propiedades CurrentItem y position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 150c358346f90a513e1558dc847ad7eb6dd6e6e2
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305964"
---
# <a name="xamarinforms-carouselview-interaction"></a>Interacción de Xamarin. Forms CarouselView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades que controlan la interacción del usuario:

- `CurrentItem`, de tipo `object`, que se muestra en el elemento actual. Esta propiedad tiene un modo de enlace predeterminado de `TwoWay`y tiene un valor `null` cuando no hay datos para mostrar.
- `CurrentItemChangedCommand`, de tipo `ICommand`, que se ejecuta cuando cambia el elemento actual.
- `CurrentItemChangedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `CurrentItemChangedCommand`.
- `IsBounceEnabled`, de tipo `bool`, que especifica si el `CarouselView` se rebotará en un límite de contenido. El valor predeterminado es `true`.
- `IsSwipeEnabled`, de tipo `bool`, que determina si un gesto de deslizar rápidamente cambiará el elemento mostrado. El valor predeterminado es `true`.
- `Position`, de tipo `int`, el índice del elemento actual de la colección subyacente. Esta propiedad tiene un modo de enlace predeterminado de `TwoWay`y tiene un valor 0 cuando no hay datos para mostrar.
- `PositionChangedCommand`, de tipo `ICommand`, que se ejecuta cuando cambia la posición.
- `PositionChangedCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `PositionChangedCommand`.
- `VisibleViews`, de tipo `ObservableCollection<View>`, que es una propiedad de solo lectura que contiene los objetos de los elementos que están visibles actualmente.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define un evento `CurrentItemChanged` que se desencadena cuando cambia la propiedad `CurrentItem`, ya sea debido al desplazamiento por el usuario, o cuando una aplicación establece la propiedad. El objeto `CurrentItemChangedEventArgs` que acompaña al evento `CurrentItemChanged` tiene dos propiedades, ambas de tipo `object`:

- `PreviousItem`: el elemento anterior, después del cambio de la propiedad.
- `CurrentItem`: el elemento actual, después del cambio de la propiedad.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también define un evento `PositionChanged` que se desencadena cuando cambia la propiedad `Position`, ya sea debido al desplazamiento por el usuario, o cuando una aplicación establece la propiedad. El objeto `PositionChangedEventArgs` que acompaña al evento `PositionChanged` tiene dos propiedades, ambas de tipo `int`:

- `PreviousPosition`: la posición anterior, después del cambio de la propiedad.
- `CurrentPosition`: la posición actual, después del cambio de la propiedad.

## <a name="respond-to-the-current-item-changing"></a>Responder al cambio del elemento actual

Cuando el elemento mostrado actualmente cambie, la propiedad `CurrentItem` se establecerá en el valor del elemento. Cuando esta propiedad cambia, el `CurrentItemChangedCommand` se ejecuta con el valor de la `CurrentItemChangedCommandParameter` que se pasa a la `ICommand`. A continuación, se actualiza la propiedad `Position` y se activa el evento `CurrentItemChanged`.

> [!IMPORTANT]
> La propiedad `Position` cambia cuando cambia la propiedad `CurrentItem`. Esto hará que el `PositionChangedCommand` se ejecute y que el evento `PositionChanged` desencadene.

### <a name="event"></a>Evento

En el ejemplo de XAML siguiente se muestra un [`CarouselView`](xref:Xamarin.Forms.CarouselView) que utiliza un controlador de eventos para responder al cambio del elemento actual:

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

En este ejemplo, el controlador de eventos `OnCurrentItemChanged` se ejecuta cuando se activa el evento `CurrentItemChanged`:

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

En este ejemplo, el controlador de eventos `OnCurrentItemChanged` expone los elementos anteriores y actuales:

[![Captura de pantalla de un CarouselView con elementos anteriores y actuales, en iOS y Android](interaction-images/current-item-events.png "CarouselView con elementos actuales y anteriores")](interaction-images/current-item-events-large.png#lightbox "CarouselView con elementos actuales y anteriores")

### <a name="command"></a>Get-Help

En el ejemplo de XAML siguiente se muestra un [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa un comando para responder al elemento actual Changing:

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

En este ejemplo, la propiedad `CurrentItemChangedCommand` se enlaza a la propiedad `ItemChangedCommand`, pasando el valor de la propiedad `CurrentItem` como argumento. A continuación, el `ItemChangedCommand` puede responder al cambio del elemento actual, según sea necesario:

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

En este ejemplo, el `ItemChangedCommand` actualiza los objetos que almacenan los elementos anteriores y actuales.

## <a name="respond-to-the-position-changing"></a>Responder al cambio de posición

Cuando el elemento mostrado actualmente cambia, la propiedad `Position` se establecerá en el índice del elemento actual de la colección subyacente. Cuando esta propiedad cambia, el `PositionChangedCommand` se ejecuta con el valor de la `PositionChangedCommandParameter` que se pasa a la `ICommand`. A continuación, se activa el evento `PositionChanged`. Si se ha cambiado mediante programación la propiedad `Position`, el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se desplazará al elemento que se corresponde con el valor `Position`.

> [!NOTE]
> Si establece la propiedad `Position` en 0, se mostrará el primer elemento de la colección subyacente.

### <a name="event"></a>Evento

En el ejemplo de XAML siguiente se muestra un [`CarouselView`](xref:Xamarin.Forms.CarouselView) que utiliza un controlador de eventos para responder al cambio de propiedad `Position`:

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

En este ejemplo, el controlador de eventos `OnPositionChanged` se ejecuta cuando se activa el evento `PositionChanged`:

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

En este ejemplo, el controlador de eventos `OnCurrentItemChanged` expone las posiciones anterior y actual:

[![Captura de pantalla de un CarouselView con las posiciones actuales y anteriores, en iOS y Android](interaction-images/current-position-events.png "CarouselView con las posiciones actual y anterior")](interaction-images/current-position-events-large.png#lightbox "CarouselView con las posiciones actual y anterior")

### <a name="command"></a>Get-Help

En el ejemplo de XAML siguiente se muestra un [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa un comando para responder al cambio de propiedad `Position`:

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

En este ejemplo, la propiedad `PositionChangedCommand` se enlaza a la propiedad `PositionChangedCommand`, pasando el valor de la propiedad `Position` como argumento. A continuación, el `PositionChangedCommand` puede responder al cambio de posición, según sea necesario:

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

En este ejemplo, el `PositionChangedCommand` actualiza los objetos que almacenan las posiciones anterior y actual.

## <a name="preset-the-current-item"></a>Predefinir el elemento actual

El elemento actual de un [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede establecer mediante programación estableciendo la propiedad `CurrentItem` en el elemento. En el ejemplo de XAML siguiente se muestra un `CarouselView` que elige previamente el elemento actual:

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
> La propiedad `CurrentItem` tiene un modo de enlace predeterminado de `TwoWay`.

Los datos de la propiedad `CarouselView.CurrentItem` se enlazan a la propiedad `CurrentItem` del modelo de vista conectado, que es de tipo `Monkey`. De forma predeterminada, se usa un enlace `TwoWay` de modo que, si el usuario cambia el elemento actual, el valor de la propiedad `CurrentItem` se establecerá en el objeto `Monkey` actual. La propiedad `CurrentItem` se define en la clase `MonkeysViewModel`:

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

En este ejemplo, la propiedad `CurrentItem` está establecida en el cuarto elemento de la colección `Monkeys`:

[![Captura de pantalla de un CarouselView con el elemento preestablecido, en iOS y Android](interaction-images/preset-item.png "CarouselView con elemento preestablecido")](interaction-images/preset-item-large.png#lightbox "CarouselView con elemento preestablecido")

## <a name="preset-the-position"></a>Valor preestablecido de la posición

El elemento mostrado [`CarouselView`](xref:Xamarin.Forms.CarouselView) se puede establecer mediante programación estableciendo la propiedad `Position` en el índice del elemento de la colección subyacente. En el ejemplo de XAML siguiente se muestra un `CarouselView` que establece el elemento mostrado:

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
> La propiedad `Position` tiene un modo de enlace predeterminado de `TwoWay`.

Los datos de la propiedad `CarouselView.Position` se enlazan a la propiedad `Position` del modelo de vista conectado, que es de tipo `int`. De forma predeterminada, se usa un enlace `TwoWay` de modo que si el usuario se desplaza por el [`CarouselView`](xref:Xamarin.Forms.CarouselView), el valor de la propiedad `Position` se establecerá en el índice del elemento mostrado. La propiedad `Position` se define en la clase `MonkeysViewModel`:

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

En este ejemplo, la propiedad `Position` está establecida en el cuarto elemento de la colección `Monkeys`:

[![Captura de pantalla de un CarouselView con la posición preestablecida, en iOS y Android](interaction-images/preset-position.png "CarouselView con posición preestablecida")](interaction-images/preset-position-large.png#lightbox "CarouselView con posición preestablecida")

## <a name="define-visual-states"></a>Definir Estados visuales

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define cuatro Estados visuales:

- `CurrentItem` representa el estado visual del elemento que se muestra actualmente.
- `PreviousItem` representa el estado visual del elemento mostrado previamente.
- `NextItem` representa el estado visual del elemento siguiente.
- `DefaultItem` representa el estado visual para el resto de los elementos.

Estos Estados visuales se pueden usar para iniciar cambios visuales en los elementos mostrados por el [`CarouselView`](xref:Xamarin.Forms.CarouselView).

En el siguiente ejemplo de XAML se muestra cómo definir los Estados visuales `CurrentItem`, `PreviousItem`, `NextItem`y `DefaultItem`:

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

En este ejemplo, el estado visual `CurrentItem` especifica que el elemento actual que muestra la [`CarouselView`](xref:Xamarin.Forms.CarouselView) tendrá su propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) cambiada de su valor predeterminado de 1 a 1,1. Los Estados visuales `PreviousItem` y `NextItem` especifican que los elementos que rodean al elemento actual se mostrarán con un valor [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) de 0,5. El estado visual `DefaultItem` especifica que el resto de los elementos mostrados por el `CarouselView` se mostrarán con un valor `Opacity` de 0,25.

> [!NOTE]
> Como alternativa, los Estados visuales se pueden definir en una [`Style`](xref:Xamarin.Forms.Style) que tenga un valor de propiedad [`TargetType`](xref:Xamarin.Forms.Style.TargetType) que sea el tipo del elemento raíz de la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que se establece como el valor de la propiedad `ItemTemplate`.

Las capturas de pantallas siguientes muestran los Estados visuales `CurrentItem`, `PreviousItem`y `NextItem`:

[![Captura de pantalla de un CarouselView con Estados visuales, en iOS y Android](interaction-images/visual-states.png "Estados visuales de CarouselView")](interaction-images/visual-states-large.png#lightbox "Estados visuales de CarouselView")

Para obtener más información sobre los Estados visuales, consulte [Visual State Manager de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Borrar el elemento actual

La propiedad `CurrentItem` se puede borrar estableciéndolo, o el objeto al que se enlaza, en `null`.

## <a name="disable-bounce"></a>Deshabilitar rebote

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rebota los elementos en los límites de contenido. Esto se puede deshabilitar estableciendo la propiedad `IsBounceEnabled` en `false`.

## <a name="disable-swipe-interaction"></a>Deshabilitar la interacción de deslizar rápidamente

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) permite a los usuarios desplazarse por los elementos con un gesto de deslizar rápidamente. Esta interacción de deslizar rápidamente puede deshabilitarse estableciendo la propiedad `IsSwipeEnabled` en `false`.

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
