---
title: Expansor de Xamarin. Forms
description: El control de expansor de Xamarin. Forms proporciona un contenedor expansible para hospedar cualquier contenido. El contenido se muestra u oculta al puntear en el encabezado del expansor.
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
ms.openlocfilehash: b1e573a6070a637ef2fdfa65bb0fc1375522fc3c
ms.sourcegitcommit: 443ecd9146fe2a7bbb9b5ab6d33c835876efcf1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2020
ms.locfileid: "82852493"
---
# <a name="xamarinforms-expander"></a>Expansor de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

El control Xamarin. `Expander` Forms proporciona un contenedor expansible para hospedar cualquier contenido. El control tiene un encabezado y contenido, y el contenido se muestra u oculta al puntear `Expander` en el encabezado. Cuando solo se `Expander` muestra el encabezado, `Expander` se *contrae*. Cuando el `Expander` contenido está visible, `Expander` se *expande*.

Las siguientes capturas de `Expander` pantallas muestran un en los Estados contraído y expandido, con cuadros rojos que indican el encabezado y el contenido:

![Captura de pantalla de un expansor en Estados contraído y expandido, en iOS y Android](expander-images/expander.png "Ampliador en iOS y Android")

> [!IMPORTANT]
> `Expander`es experimental actualmente y solo se puede usar si se establece `Expander_Experimental` la marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).
>
> Además, el `Expander` control está totalmente implementado en el `Xamarin.Forms` espacio de nombres. Por lo tanto, está disponible en todas las plataformas compatibles con Xamarin. Forms.

El `Expander` control define las siguientes propiedades:

- `CollapseAnimationEasing`, de tipo [`Easing`](xref:Xamarin.Forms.Easing), que representa la función de aceleración que se va a `Expander` aplicar al contenido cuando se contrae.
- `CollapseAnimationLength`, de tipo `uint`, que define la duración de la animación cuando `Expander` se contrae. El valor predeterminado de esta propiedad es 250 ms.
- `Command`, de tipo `ICommand`, que se ejecuta cuando se `Expander` puntea el encabezado.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `Content`, de tipo [`View`](xref:Xamarin.Forms.View), que define el contenido que se va a mostrar `Expander` cuando se expande el control.
- `ContentTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que es la plantilla que se usa para aumentar de forma dinámica el contenido de `Expander`.
- `ExpandAnimationEasing`, de tipo [`Easing`](xref:Xamarin.Forms.Easing), que representa la función de aceleración que se va a `Expander` aplicar al contenido durante la expansión.
- `ExpandAnimationLength`, de tipo `uint`, que define la duración de la animación cuando se `Expander` expande. El valor predeterminado de esta propiedad es 250 ms.
- `ForceUpdateSizeCommand`, de tipo `ICommand`, que define el comando que `Expander` se ejecuta cuando se fuerza la actualización del tamaño de. Esta propiedad utiliza el `OneWayToSource` modo de enlace.
- `Header`, de tipo [`View`](xref:Xamarin.Forms.View), que define el contenido del encabezado.
- `IsExpanded`, de tipo `bool`, que determina si `Expander` se expande. Esta propiedad utiliza el `TwoWay` modo de enlace y tiene un valor predeterminado de `false`.
- `Spacing`, de tipo `double`, que representa el espacio entre el encabezado y su contenido. El valor predeterminado de esta propiedad es 0.
- `State`, de tipo `ExpanderState`, que representa el estado de `Expander`. Esta propiedad utiliza el `OneWayToSource` modo de enlace.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> La `Content` propiedad es la propiedad de contenido de `Expander` la clase y, por tanto, no es necesario establecer explícitamente desde XAML.

La enumeración `ExpanderState` define los miembros siguientes:

- `Expanding`indica que `Expander` se está expandiendo.
- `Expanded`indica que `Expander` está expandido.
- `Collapsing`indica que `Expander` se contrae.
- `Collapsed`indica que `Expander` está contraído.

El `Expander` control también define un `Tapped` evento que se desencadena cuando se `Expander` puntea el encabezado. Además, `Expander` incluye un `ForceUpdateSize` método al que se puede llamar para cambiar mediante programación el `Expander` tamaño de en tiempo de ejecución.

## <a name="create-an-expander"></a>Crear un expansor

En el ejemplo siguiente se muestra cómo crear una `Expander` instancia de en XAML:

```xaml
<Expander>
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

En este ejemplo, `Expander` se contrae de forma predeterminada y muestra [`Label`](xref:Xamarin.Forms.Label) como su encabezado. Al pulsar en el encabezado, se `Expander` expande el contenido para mostrar su contenido, que [`Grid`](xref:Xamarin.Forms.Grid) es un contenedor que contiene controles secundarios. Cuando se `Expander` expande el, al puntear en el encabezado `Expander`, se contrae.

> [!IMPORTANT]
> Al establecer la `Expander.Content` propiedad, ya sea implícita o explícitamente, `Expander` el contenido se crea cuando se navega a la página que lo contiene, incluso si `Expander` está contraído. Sin embargo, `Expander.ContentTemplate` la propiedad se puede establecer en el contenido que solo se aumenta cuando se `Expander` expande por primera vez. Para obtener más información, consulte [crear contenido de expansión a petición](#create-expander-content-on-demand).

Como alternativa, se `Expander` puede crear en el código:

```csharp
Expander expander = new Expander
{
    Header = new Label
    {
        Text = "Baboon",
        FontAttributes = FontAttributes.Bold,
        FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label))
    }
};

Grid grid = new Grid
{
    Padding = new Thickness(10),
    ColumnDefinitions =
    {
        new ColumnDefinition { Width = GridLength.Auto },
        new ColumnDefinition { Width = GridLength.Auto }
    }
};

grid.Children.Add(new Image
{
    Source = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg",
    Aspect = Aspect.AspectFill,
    HeightRequest = 120,
    WidthRequest = 120
});

grid.Children.Add(new Label
{
    Text = "Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae.",
    FontAttributes = FontAttributes.Italic
}, 1, 0);

expander.Content = grid;
```

## <a name="create-expander-content-on-demand"></a>Creación de contenido de expansión a petición

`Expander`el contenido se puede crear a petición, en respuesta a `Expander` la expansión. Esto puede realizarse estableciendo la `Expander.ContentTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contiene el contenido:

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

En este ejemplo, el `Expander` contenido se infla únicamente cuando se `Expander` expande por primera vez.

La ventaja de este enfoque es que cuando una página contiene varios `Expander` objetos, el contenido de un `Expander` solo se crea cuando lo expande el usuario por primera vez.

## <a name="add-an-expansion-indicator"></a>Agregar un indicador de expansión

Se [`Image`](xref:Xamarin.Forms.Image) puede Agregar un a un `Expander` encabezado para proporcionar una indicación visual del estado de expansión. Se [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) puede asociar a `Image`, que cambia la `Source` propiedad basándose en el valor de la `Expander.IsExpanded` propiedad:

```xaml
<Expander>
    <Expander.Header>
        <Grid>
            <Label Text="{Binding Name}"
                   FontAttributes="Bold"
                   FontSize="Medium" />
            <Image Source="expand.png"
                   HorizontalOptions="End"
                   VerticalOptions="Start">
                <Image.Triggers>
                    <DataTrigger TargetType="Image"
                                 Binding="{Binding Source={RelativeSource AncestorType={x:Type Expander}}, Path=IsExpanded}"
                                 Value="True">
                        <Setter Property="Source"
                                Value="collapse.png" />
                    </DataTrigger>
                </Image.Triggers>
            </Image>
        </Grid>
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

En este ejemplo, [`Image`](xref:Xamarin.Forms.Image) muestra el `expand` icono de forma predeterminada:

![Captura de pantalla de un icono de expansión en estado contraído, en iOS y Android](expander-images/icon-expand.png "Icono expandido en iOS y Android")

La `IsExpanded` propiedad se `true` convierte en `Expander` cuando se puntea el encabezado, lo que `collapse` hace que se muestre el icono:

![Captura de pantalla de un icono de expansión en estado de expansión, en iOS y Android](expander-images/icon-collapse.png "Icono expandido en iOS y Android")

Para obtener más información sobre los desencadenadores, consulte los [desencadenadores de Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="define-the-space-between-header-and-content"></a>Definir el espacio entre el encabezado y el contenido

De forma predeterminada, el contenido de `Expander` un aparece directamente debajo de su encabezado. Sin embargo, este comportamiento se puede cambiar estableciendo la `Spacing` propiedad en un `double` valor que representa el espacio vacío entre el contenido y su encabezado:

```xaml
<Expander Spacing="50"
          IsExpanded="true">
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

En este ejemplo, el `Expander` contenido muestra unidades independientes del dispositivo 50 debajo del encabezado:

![Captura de pantalla de un expansor con un conjunto de espaciado, en iOS y Android](expander-images/expander-spacing.png "Expansor con espacio establecido en iOS y Android")

## <a name="embed-an-expander-in-an-expander"></a>Insertar un expansor en un expansor

El contenido de se `Expander` puede establecer en otro `Expander` control para habilitar varios niveles de expansión. En el código XAML siguiente `Expander` se muestra un cuyo `Expander` contenido es otro objeto:

```xaml
<Expander Spacing="10">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander Padding="10"
              Spacing="10">
        <Expander.Header>
            <Label Text="{Binding Location}"
                   FontSize="Medium" />
        </Expander.Header>
            <Expander.ContentTemplate>
                <DataTemplate>
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="120"
                               WidthRequest="120" />
                        <Label Grid.Column="1"
                               Text="{Binding Details}"
                               FontAttributes="Italic" />
                    </Grid>
                </DataTemplate>
            </Expander.ContentTemplate>
    </Expander>
</Expander>
```

En este ejemplo, al pulsar el `Expander` encabezado raíz se revela el encabezado del `Expander`elemento secundario:

![Captura de pantalla de un expansor incrustado, en iOS y Android](expander-images/embedded-expander1.png "Ampliador incrustado en iOS y Android")

Al pulsar el `Expander` encabezado secundario, el contenido se infla y muestra:

![Captura de pantalla de un expansor incrustado, en iOS y Android](expander-images/embedded-expander2.png "Ampliador incrustado en iOS y Android")

## <a name="define-the-expand-and-collapse-animation"></a>Definir la animación de expandir y contraer

La animación que se produce cuando `Expander` se expande o contrae se puede definir mediante el establecimiento `ExpandAnimationEasing` de `CollapseAnimationEasing` las propiedades y en cualquiera de las funciones de aceleración incluidas en Xamarin. Forms o en las funciones de aceleración personalizadas. De forma predeterminada, las animaciones de expandir y contraer se producen sobre 250 ms. Sin embargo, estas duraciones se pueden cambiar estableciendo las `ExpandAnimationLength` propiedades `CollapseAnimationLength` y en `uint` valores.

En el código XAML siguiente `Expander` se muestra un ejemplo de la definición de la animación que se produce cuando el usuario expande o contrae el usuario:

```xaml
<Expander ExpandAnimationEasing="{x:Static Easing.CubicIn}"
          ExpandAnimationLength="500"
          CollapseAnimationEasing="{x:Static Easing.CubicOut}"
          CollapseAnimationLength="500">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

En este ejemplo, la `CubicIn` función de entradas y salidas lentas acelera lentamente la animación de expansión `CubicOut` a través de 500 ms y la función de aceleración reduce rápidamente la animación de contraer a través de 500 ms.

Para obtener más información sobre las funciones de aceleración, consulte [funciones de aceleración de Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="resize-an-expander-at-runtime"></a>Cambiar el tamaño de un expansor en tiempo de ejecución

Se `Expander` puede cambiar el tamaño mediante programación en tiempo de ejecución con `ForceUpdateSize` el método.

Dado un `Expander` denominado `expander`, cuyo contenido incluye un [`Label`](xref:Xamarin.Forms.Label) que tiene un `TapGestureRecognizer` adjunto, en el ejemplo de código siguiente se muestra la `ForceUpdateSize` llamada al método:

```csharp
void OnLabelTapped(object sender, EventArgs e)
{
    Label label = sender as Label;
    Expander expander = label.Parent.Parent.Parent as Expander;

    if (label.FontSize == Device.GetNamedSize(NamedSize.Default, label))
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Large, label);
    }
    else
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Default, label);
    }
    expander.ForceUpdateSize();
}
```

En este ejemplo, el `FontSize` de un [`Label`](xref:Xamarin.Forms.Label) cambia cuando `Label` se puntea. Debido al tamaño del cambio de fuente, es necesario actualizar el tamaño de `Expander` llamando a su `ForceUpdateSize` método.

## <a name="disable-an-expander"></a>Deshabilitar un expansor

Una aplicación puede entrar en un estado en el `Expander` que la expansión de no es una operación válida. En tales casos, se `Expander` puede deshabilitar estableciendo su `IsEnabled` propiedad en false. Esto impedirá que los usuarios expandan `Expander`o contraigan.

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Expander (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Funciones de aceleración de Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md)
- [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Diseños enlazables de Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
