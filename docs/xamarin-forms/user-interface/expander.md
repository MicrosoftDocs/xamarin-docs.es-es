---
title: Xamarin.FormsExpansor
description: El Xamarin.Forms control EXPANDER proporciona un contenedor expansible para hospedar cualquier contenido. El contenido se muestra u oculta al puntear en el encabezado del expansor.
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e9afa0f6d27003891963af5715d5721e3129306
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84129589"
---
# <a name="xamarinforms-expander"></a>Xamarin.FormsExpansor

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

El Xamarin.Forms `Expander` control proporciona un contenedor expansible para hospedar cualquier contenido. El control tiene un encabezado y contenido, y el contenido se muestra u oculta al puntear en el `Expander` encabezado. Cuando solo `Expander` se muestra el encabezado, `Expander` se *contrae*. Cuando el `Expander` contenido está visible, `Expander` se *expande*.

Las siguientes capturas de pantallas muestran un `Expander` en los Estados contraído y expandido, con cuadros rojos que indican el encabezado y el contenido:

![Captura de pantalla de un expansor en Estados contraído y expandido, en iOS y Android](expander-images/expander.png "Ampliador en iOS y Android")

> [!IMPORTANT]
> `Expander`es experimental actualmente y solo se puede usar si se establece la `Expander_Experimental` marca. Para obtener más información, vea [indicadores experimentales](~/xamarin-forms/internals/experimental-flags.md).
>
> Además, el `Expander` control está totalmente implementado en el `Xamarin.Forms` espacio de nombres. Por lo tanto, está disponible en todas las plataformas compatibles con Xamarin.Forms .

El `Expander` control define las siguientes propiedades:

- `CollapseAnimationEasing`, de tipo [`Easing`](xref:Xamarin.Forms.Easing) , que representa la función de aceleración que se va a aplicar al `Expander` contenido cuando se contrae.
- `CollapseAnimationLength`, de tipo `uint` , que define la duración de la animación cuando `Expander` se contrae. El valor predeterminado de esta propiedad es 250 ms.
- `Command`, de tipo `ICommand` , que se ejecuta cuando `Expander` se puntea el encabezado.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `Content`, de tipo [`View`](xref:Xamarin.Forms.View) , que define el contenido que se va a mostrar cuando se expande el control `Expander` .
- `ContentTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que es la plantilla que se usa para aumentar de forma dinámica el contenido de `Expander` .
- `ExpandAnimationEasing`, de tipo [`Easing`](xref:Xamarin.Forms.Easing) , que representa la función de aceleración que se va a aplicar al `Expander` contenido durante la expansión.
- `ExpandAnimationLength`, de tipo `uint` , que define la duración de la animación cuando se `Expander` expande. El valor predeterminado de esta propiedad es 250 ms.
- `ForceUpdateSizeCommand`, de tipo `ICommand` , que define el comando que se ejecuta cuando se fuerza la actualización del tamaño de `Expander` . Esta propiedad utiliza el `OneWayToSource` modo de enlace.
- `Header`, de tipo [`View`](xref:Xamarin.Forms.View) , que define el contenido del encabezado.
- `IsExpanded`, de tipo `bool` , que determina si `Expander` se expande. Esta propiedad utiliza el `TwoWay` modo de enlace y tiene un valor predeterminado de `false` .
- `Spacing`, de tipo `double` , que representa el espacio entre el encabezado y su contenido. El valor predeterminado de esta propiedad es 0.
- `State`, de tipo `ExpanderState` , que representa el estado de `Expander` . Esta propiedad utiliza el `OneWayToSource` modo de enlace.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> La `Content` propiedad es la propiedad de contenido de la `Expander` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

La enumeración `ExpanderState` define los miembros siguientes:

- `Expanding`indica que `Expander` se está expandiendo.
- `Expanded`indica que `Expander` está expandido.
- `Collapsing`indica que `Expander` se contrae.
- `Collapsed`indica que `Expander` está contraído.

El `Expander` control también define un `Tapped` evento que se desencadena cuando `Expander` se puntea el encabezado. Además, `Expander` incluye un `ForceUpdateSize` método al que se puede llamar para cambiar mediante programación el tamaño de `Expander` en tiempo de ejecución.

## <a name="create-an-expander"></a>Crear un expansor

En el ejemplo siguiente se muestra cómo crear una instancia `Expander` de en XAML:

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

En este ejemplo, `Expander` se contrae de forma predeterminada y muestra [`Label`](xref:Xamarin.Forms.Label) como su encabezado. Al pulsar en el encabezado `Expander` , se expande el contenido para mostrar su contenido, que es un [`Grid`](xref:Xamarin.Forms.Grid) contenedor que contiene controles secundarios. Cuando `Expander` se expande el, al puntear en el encabezado, se contrae `Expander` .

> [!IMPORTANT]
> Al establecer la `Expander.Content` propiedad, ya sea implícita o explícitamente, el `Expander` contenido se crea cuando se navega a la página que lo contiene, incluso si `Expander` está contraído. Sin embargo, la `Expander.ContentTemplate` propiedad se puede establecer en el contenido que solo se aumenta cuando se `Expander` expande por primera vez. Para obtener más información, consulte [crear contenido de expansión a petición](#create-expander-content-on-demand).

Como alternativa, `Expander` se puede crear en el código:

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

`Expander`el contenido se puede crear a petición, en respuesta a la `Expander` expansión. Esto puede realizarse estableciendo la `Expander.ContentTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que contiene el contenido:

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

[`Image`](xref:Xamarin.Forms.Image)Se puede Agregar un a un `Expander` encabezado para proporcionar una indicación visual del estado de expansión. [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)Se puede asociar a `Image` , que cambia la `Source` propiedad basándose en el valor de la `Expander.IsExpanded` propiedad:

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

La `IsExpanded` propiedad se convierte `true` `Expander` en cuando se puntea el encabezado, lo que hace que se muestre el `collapse` icono:

![Captura de pantalla de un icono de expansión en estado de expansión, en iOS y Android](expander-images/icon-collapse.png "Icono expandido en iOS y Android")

Para obtener más información acerca de los desencadenadores, vea [ Xamarin.Forms desencadenadores](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="define-the-space-between-header-and-content"></a>Definir el espacio entre el encabezado y el contenido

De forma predeterminada, el contenido de un `Expander` aparece directamente debajo de su encabezado. Sin embargo, este comportamiento se puede cambiar estableciendo la `Spacing` propiedad en un `double` valor que representa el espacio vacío entre el contenido y su encabezado:

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

El contenido de `Expander` se puede establecer en otro `Expander` control para habilitar varios niveles de expansión. En el código XAML siguiente `Expander` se muestra un cuyo contenido es otro `Expander` objeto:

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

En este ejemplo, al pulsar el `Expander` encabezado raíz se revela el encabezado del elemento secundario `Expander` :

![Captura de pantalla de un expansor incrustado, en iOS y Android](expander-images/embedded-expander1.png "Ampliador incrustado en iOS y Android")

Al pulsar el `Expander` encabezado secundario, el contenido se infla y muestra:

![Captura de pantalla de un expansor incrustado, en iOS y Android](expander-images/embedded-expander2.png "Ampliador incrustado en iOS y Android")

## <a name="define-the-expand-and-collapse-animation"></a>Definir la animación de expandir y contraer

La animación que se produce cuando se `Expander` expande o se contrae se puede definir mediante el establecimiento de las `ExpandAnimationEasing` `CollapseAnimationEasing` propiedades y en cualquiera de las funciones de aceleración incluidas en Xamarin.Forms o en las funciones de aceleración personalizadas. De forma predeterminada, las animaciones de expandir y contraer se producen sobre 250 ms. Sin embargo, estas duraciones se pueden cambiar estableciendo las `ExpandAnimationLength` `CollapseAnimationLength` propiedades y en `uint` valores.

En el código XAML siguiente se muestra un ejemplo de la definición de la animación que se produce cuando el `Expander` usuario expande o contrae el usuario:

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

En este ejemplo, la `CubicIn` función de entradas y salidas lentas acelera lentamente la animación de expansión a través de 500 ms y la `CubicOut` función de aceleración reduce rápidamente la animación de contraer a través de 500 ms.

Para obtener más información sobre las funciones de aceleración, consulte [ Xamarin.Forms funciones de aceleración](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="resize-an-expander-at-runtime"></a>Cambiar el tamaño de un expansor en tiempo de ejecución

`Expander`Se puede cambiar el tamaño mediante programación en tiempo de ejecución con el `ForceUpdateSize` método.

Dado un `Expander` denominado `expander` , cuyo contenido incluye un [`Label`](xref:Xamarin.Forms.Label) que tiene un `TapGestureRecognizer` adjunto, en el ejemplo de código siguiente se muestra la llamada al `ForceUpdateSize` método:

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

En este ejemplo, el `FontSize` de un [`Label`](xref:Xamarin.Forms.Label) cambia cuando `Label` se puntea. Debido al tamaño del cambio de fuente, es necesario actualizar el tamaño de llamando a `Expander` su `ForceUpdateSize` método.

## <a name="disable-an-expander"></a>Deshabilitar un expansor

Una aplicación puede entrar en un estado en el que `Expander` la expansión de no es una operación válida. En tales casos, `Expander` se puede deshabilitar estableciendo su `IsEnabled` propiedad en false. Esto impedirá que los usuarios expandan o contraigan `Expander` .

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de Expander (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Xamarin.FormsFunciones de aceleración](~/xamarin-forms/user-interface/animation/easing.md)
- [Desencadenadores de Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsDiseños enlazables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
