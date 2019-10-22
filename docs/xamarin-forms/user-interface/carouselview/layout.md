---
title: Diseño de Xamarin. Forms CarouselView
description: De forma predeterminada, un CarouselView mostrará los elementos horizontalmente. Sin embargo, también es posible una orientación vertical.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: 7aa0c5833682518b02fca9fe73a9f69b168e60eb
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696963"
---
# <a name="xamarinforms-carouselview-layout"></a>Diseño de Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades que controlan el diseño:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de tipo `LinearItemsLayout`, especifica el diseño que se va a utilizar.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), de tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), especifica la estrategia de medida de elementos que se va a utilizar.
- `NumberOfSideItems`, de tipo `int`, el número de elementos visibles adyacentes al elemento actual. El valor predeterminado es 0.
- `PeekAreaInsets`, de tipo [`Thickness`](xref:Xamarin.Forms.Thickness), especifica la cantidad de espacio que pueden ver los elementos adyacentes parcialmente.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

De forma predeterminada, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) mostrará sus elementos en una orientación horizontal. Se mostrará un solo elemento en la pantalla, con gestos de deslizar rápidamente, lo que da como resultado la navegación hacia delante y hacia atrás a través de la colección de elementos. Sin embargo, también es posible una orientación vertical. Esto se debe a que la propiedad [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) es de tipo `LinearItemsLayout`, que hereda de la clase [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) . La clase `ItemsLayout` define las siguientes propiedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica la dirección en la que el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expande a medida que se agregan elementos.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de ajuste con los elementos.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de los puntos de ajuste al desplazarse.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos. Para obtener más información sobre los puntos de acoplamiento, vea [puntos de acoplamiento](scrolling.md#snap-points) en la guía de [desplazamientos de la CollectionView de Xamarin. Forms](scrolling.md) .

La enumeración [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) define los siguientes miembros:

- `Vertical` indica que el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expandirá verticalmente a medida que se agreguen elementos.
- `Horizontal` indica que el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expandirá horizontalmente a medida que se agreguen elementos.

La clase `LinearItemsLayout` hereda de la clase [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) y define una propiedad `ItemSpacing`, de tipo `double`, que representa el espacio vacío alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0. La clase `LinearItemsLayout` también define los miembros `Vertical` y `Horizontal` estáticos. Estos miembros se pueden usar para crear listas verticales u horizontales, respectivamente. Como alternativa, se puede crear un objeto de `LinearItemsLayout`, especificando un miembro de enumeración [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como un argumento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) usa los motores de diseño nativo para realizar el diseño.

## <a name="horizontal-layout"></a>Diseño horizontal

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) mostrará los elementos horizontalmente. Por lo tanto, no es necesario establecer la propiedad [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) para usar este diseño:

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

También puede realizar este diseño estableciendo la propiedad [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) en un objeto `LinearItemsLayout`, especificando el miembro de enumeración `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como el valor de la propiedad `Orientation`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

Esto da como resultado un diseño que crece horizontalmente a medida que se agregan nuevos elementos.

## <a name="vertical-layout"></a>Diseño vertical

[`CarouselView`](xref:Xamarin.Forms.CarouselView) puede mostrar sus elementos verticalmente estableciendo la propiedad [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) en un objeto `LinearItemsLayout`, especificando el miembro de enumeración [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) `Vertical` como el valor de la propiedad `Orientation`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CarouselView.ItemsLayout>
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
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

Esto da como resultado un diseño que crece verticalmente a medida que se agregan nuevos elementos.

## <a name="partially-visible-adjacent-items"></a>Elementos adyacentes parcialmente visibles

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) muestra todos los elementos a la vez. Sin embargo, este comportamiento se puede cambiar estableciendo la propiedad `PeekAreaInsets` en un valor `Thickness` que especifica la cantidad de tiempo que pueden ver los elementos adyacentes parcialmente. Esto puede ser útil para indicar a los usuarios que hay elementos adicionales que ver. En el código XAML siguiente se muestra un ejemplo de cómo establecer esta propiedad:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    PeekAreaInsets = new Thickness(100)
};
```

El resultado es que los elementos adyacentes se exponen parcialmente en la pantalla.

## <a name="fully-visible-adjacent-items"></a>Elementos adyacentes totalmente visibles

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) muestra un elemento cada vez. Sin embargo, este comportamiento se puede cambiar estableciendo la propiedad `NumberOfSideItems` en un entero que representa el número de elementos que se van a mostrar junto al elemento actual. En el código XAML siguiente se muestra un ejemplo de cómo establecer esta propiedad:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    NumberOfSideItems = 1
};
```

Este ejemplo da como resultado que se muestre un elemento adyacente en cada lado del elemento actual.

> [!NOTE]
> Al establecer la propiedad `NumberOfSideItems`, se sigue aplicando cualquier valor `PeekAreaInsets`.

## <a name="item-spacing"></a>Espaciado de elementos

De forma predeterminada, cada elemento de una [`CarouselView`](xref:Xamarin.Forms.CarouselView) no tiene espacio vacío. Este comportamiento se puede cambiar estableciendo las propiedades en el diseño de los elementos utilizados por el `CarouselView`.

Cuando un [`CarouselView`](xref:Xamarin.Forms.CarouselView) establece su propiedad [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) en un objeto `LinearItemsLayout`, la propiedad `LinearItemsLayout.ItemSpacing` se puede establecer en un valor `double` que representa el espacio vacío alrededor de cada elemento:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

> [!NOTE]
> La propiedad `LinearItemsLayout.ItemSpacing` tiene un conjunto de devoluciones de llamada de validación, que garantiza que el valor de la propiedad sea siempre mayor o igual que 0.

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Este código genera un diseño vertical, que tiene un espaciado de 20 alrededor de cada elemento.

## <a name="dynamic-resizing-of-items"></a>Cambio de tamaño dinámico de los elementos

Los elementos de un [`CarouselView`](xref:Xamarin.Forms.CarouselView) pueden cambiar de tamaño dinámicamente en tiempo de ejecución cambiando las propiedades relacionadas con el diseño de los elementos de la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Por ejemplo, en el ejemplo de código siguiente se cambian las propiedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) de un objeto [`Image`](xref:Xamarin.Forms.Image) y la propiedad `HeightRequest` de su [`Frame`](xref:Xamarin.Forms.Frame)primario:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

El controlador de eventos `OnImageTapped` se ejecuta como respuesta a un objeto [`Image`](xref:Xamarin.Forms.Image) que se está punteando y cambia las dimensiones de la imagen (y su marco primario), para que se vea más fácilmente.

## <a name="right-to-left-layout"></a>Diseño de derecha a izquierda

[`CarouselView`](xref:Xamarin.Forms.CarouselView) puede diseñar su contenido en una dirección de flujo de derecha a izquierda estableciendo su propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) en [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft). Sin embargo, la propiedad `FlowDirection` debe establecerse idealmente en un diseño de página o raíz, lo que hace que todos los elementos de la página o el diseño raíz respondan a la dirección del flujo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CarouselViewDemos.Views.HorizontalTemplateLayoutRTLPage"
             Title="Horizontal layout (RTL FlowDirection)"
             FlowDirection="RightToLeft">    
    <CarouselView ItemsSource="{Binding Monkeys}">
        ...
    </CarouselView>
</ContentPage>
```

La [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predeterminada para un elemento con un elemento primario es [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, el [`CarouselView`](xref:Xamarin.Forms.CarouselView) hereda el valor de la propiedad `FlowDirection` de la [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Desplazamiento de CarouselView de Xamarin. Forms](scrolling.md)
