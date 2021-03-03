---
title: Xamarin.Forms IndicatorView
description: IndicatorView es un control que muestra los indicadores que representan el número de elementos y la posición actual, en un CarouselView.
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/24/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 27ee2892a25a86210bc86ad4d329fd1f1ca2c788
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751419"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.Forms IndicatorView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)Es un control que muestra los indicadores que representan el número de elementos y la posición actual en un [`CarouselView`](xref:Xamarin.Forms.CarouselView) :

[![Captura de pantalla de CarouselView y IndicatorView en iOS y Android](indicatorview-images/circles.png "Círculos IndicatorView")](indicatorview-images/circles-large.png#lightbox "Círculos IndicatorView")

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView) define las siguientes propiedades:

- `Count`, de tipo `int` , el número de indicadores.
- `HideSingle`, de tipo `bool` , indica si el indicador debe ocultarse cuando solo existe uno. El valor predeterminado es `true`.
- `IndicatorColor`, de tipo `Color` , el color de los indicadores.
- `IndicatorSize`, de tipo `double` , el tamaño de los indicadores. El valor predeterminado es 6,0.
- `IndicatorLayout`, de tipo `Layout<View>` , define la clase de diseño utilizada para representar [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) . Esta propiedad está establecida por Xamarin.Forms y, por lo general, no es necesario que la configuren los desarrolladores.
- `IndicatorTemplate`, de tipo `DataTemplate` , la plantilla que define la apariencia de cada indicador.
- `IndicatorsShape`, de tipo `IndicatorShape` , la forma de cada indicador.
- `ItemsSource`, de tipo `IEnumerable` , la colección para la que se mostrarán los indicadores. Esta propiedad se establecerá automáticamente cuando `CarouselView.IndicatorView` se establezca la propiedad.
- `MaximumVisible`, de tipo `int` , el número máximo de indicadores visibles. El valor predeterminado es `int.MaxValue`.
- `Position`, de tipo `int` , el índice del indicador actualmente seleccionado. Esta propiedad usa un `TwoWay` enlace. Esta propiedad se establecerá automáticamente cuando `CarouselView.IndicatorView` se establezca la propiedad.
- `SelectedIndicatorColor`, de tipo `Color` , el color del indicador que representa el elemento actual del objeto [`CarouselView`](xref:Xamarin.Forms.CarouselView) .

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

## <a name="create-an-indicatorview"></a>Creación de un IndicatorView

En el ejemplo siguiente se muestra cómo crear una instancia [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) de en XAML:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

En este ejemplo, [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) se representa debajo de [`CarouselView`](xref:Xamarin.Forms.CarouselView) , con un indicador para cada elemento en `CarouselView` . `IndicatorView`Se rellena con datos estableciendo la `CarouselView.IndicatorView` propiedad en el `IndicatorView` objeto. Cada indicador es un círculo gris claro, mientras que el indicador que representa el elemento actual del `CarouselView` es gris oscuro.

> [!IMPORTANT]
> Al establecer la propiedad `CarouselView.IndicatorView` , se obtiene el enlace de la `IndicatorView.Position` propiedad a la `CarouselView.Position` propiedad y la `IndicatorView.ItemsSource` propiedad se enlaza a la `CarouselView.ItemsSource` propiedad.

## <a name="change-indicator-shape"></a>Cambiar forma de indicador

La [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) clase tiene una `IndicatorsShape` propiedad, que determina la forma de los indicadores. Esta propiedad se puede establecer en uno de los `IndicatorShape` miembros de enumeración:

- `Circle` Especifica que las formas de indicador serán circulares. Este es el valor predeterminado de la propiedad `IndicatorView.IndicatorsShape`.
- `Square` indica que las formas de indicador serán cuadradas.

En el ejemplo siguiente se muestra una [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) configurada para usar indicadores cuadrados:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>Cambiar tamaño del indicador

La [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) clase tiene una `IndicatorSize` propiedad, de tipo `double` , que determina el tamaño de los indicadores en unidades independientes del dispositivo. El valor predeterminado de esta propiedad es 6,0.

En el ejemplo siguiente se muestra una [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) configurada para mostrar indicadores mayores:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>Limitar el número de indicadores mostrados

La [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) clase tiene una `MaximumVisible` propiedad, de tipo `int` , que determina el número máximo de indicadores visibles.

En el ejemplo siguiente se muestra una [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) configurada para mostrar un máximo de seis indicadores:

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>Definir la apariencia del indicador

La apariencia de cada indicador puede definirse estableciendo la `IndicatorView.IndicatorTemplate` propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   Margin="0,0,0,40"
                   IndicatorColor="Transparent"
                   SelectedIndicatorColor="Transparent"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Label Text="&#xf30c;"
                       FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

Los elementos especificados en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada indicador. En este ejemplo, cada indicador es un [`Label`](xref:Xamarin.Forms.Label) que muestra un icono de fuente.

Las capturas de pantallas siguientes muestran los indicadores representados mediante un icono de fuente:

[![Captura de pantalla de un IndicatorView con plantilla, en iOS y Android](indicatorview-images/templated.png "IndicatorView con plantilla")](indicatorview-images/templated-large.png#lightbox "IndicatorView con plantilla")

## <a name="set-visual-states"></a>Establecer Estados visuales

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView) tiene un `Selected` estado visual que se puede utilizar para iniciar un cambio visual en el indicador de la posición actual en `IndicatorView` . Un caso de uso común para esto [`VisualState`](xref:Xamarin.Forms.VisualState) es cambiar el color del indicador que representa la posición actual:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style x:Key="IndicatorLabelStyle"
               TargetType="Label">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="LightGray" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Black" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <IndicatorView x:Name="indicatorView"
                       Margin="0,0,0,40"
                       IndicatorColor="Transparent"
                       SelectedIndicatorColor="Transparent"
                       HorizontalOptions="Center">
            <IndicatorView.IndicatorTemplate>
                <DataTemplate>
                    <Label Text="&#xf30c;"
                           FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}"
                           Style="{StaticResource IndicatorLabelStyle}" />
                </DataTemplate>
            </IndicatorView.IndicatorTemplate>
        </IndicatorView>
    </StackLayout>
</ContentPage>
```

En este ejemplo, el `Selected` estado visual especifica que el indicador que representa la posición actual tendrá su [`TextColor`](xref:Xamarin.Forms.Label.TextColor) establecido en negro. De lo contrario, el `TextColor` del indicador será gris claro:

![Captura de pantalla del estado visual seleccionado de IndicatorView](indicatorview-images/visual-state.png)

Para obtener más información sobre los estados visuales, vea [Administrador de estado visual de Xamarin.Forms](visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [IndicatorView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md)
- [Administrador de estado visual de Xamarin.Forms](visual-state-manager.md)
