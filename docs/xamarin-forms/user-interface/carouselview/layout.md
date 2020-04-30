---
title: Diseño de Xamarin. Forms CarouselView
description: De forma predeterminada, un CarouselView mostrará los elementos horizontalmente. Sin embargo, también es posible una orientación vertical.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
ms.openlocfilehash: 3242148fa97e6b3795b57b2fed86f3643a5ecdf6
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517418"
---
# <a name="xamarinforms-carouselview-layout"></a>Diseño de Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define las siguientes propiedades que controlan el diseño:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de tipo `LinearItemsLayout`, especifica el diseño que se va a utilizar.
- `PeekAreaInsets`, de tipo [`Thickness`](xref:Xamarin.Forms.Thickness), especifica la cantidad de tiempo que pueden ver los elementos adyacentes parcialmente.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

De forma predeterminada, [`CarouselView`](xref:Xamarin.Forms.CarouselView) un mostrará sus elementos en una orientación horizontal. Se mostrará un solo elemento en la pantalla, con gestos de deslizar rápidamente, lo que da como resultado la navegación hacia delante y hacia atrás a través de la colección de elementos. Sin embargo, también es posible una orientación vertical. Esto se debe a [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) que la propiedad es `LinearItemsLayout`de tipo, que hereda de [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) la clase. La `ItemsLayout` clase define las siguientes propiedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica la dirección en la que [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expande a medida que se agregan elementos.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de ajuste con los elementos.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de los puntos de ajuste al desplazarse.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos. Para obtener más información sobre los puntos de acoplamiento, vea [puntos de acoplamiento](scrolling.md#snap-points) en la guía de [desplazamientos de la CollectionView de Xamarin. Forms](scrolling.md) .

La [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumeración define los siguientes miembros:

- `Vertical`indica que [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expandirá verticalmente a medida que se agreguen elementos.
- `Horizontal`indica que [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expandirá horizontalmente a medida que se agreguen elementos.

La `LinearItemsLayout` clase hereda de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) clase y define una `ItemSpacing` propiedad, de tipo `double`, que representa el espacio vacío alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0. La `LinearItemsLayout` clase también define los `Vertical` miembros `Horizontal` y estáticos. Estos miembros se pueden usar para crear listas verticales u horizontales, respectivamente. Como alternativa, se `LinearItemsLayout` puede crear un objeto, especificando un [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de enumeración como argumento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)usa los motores de diseño nativo para realizar el diseño.

## <a name="horizontal-layout"></a>Diseño horizontal

De forma predeterminada [`CarouselView`](xref:Xamarin.Forms.CarouselView) , mostrará sus elementos horizontalmente. Por lo tanto, no es necesario establecer la [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propiedad para utilizar este diseño:

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

Como alternativa, este diseño también puede realizarse estableciendo [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) la propiedad en un `LinearItemsLayout` objeto, especificando el `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de la enumeración `Orientation` como el valor de la propiedad:

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

Esto da como resultado un diseño que crece horizontalmente a medida que se agregan nuevos elementos:

[![Captura de pantalla de un diseño horizontal de CarouselView, en iOS y Android](layout-images/horizontal.png "CarouselView diseño horizontal")](layout-images/horizontal-large.png#lightbox "CarouselView diseño horizontal")

## <a name="vertical-layout"></a>Diseño vertical

[`CarouselView`](xref:Xamarin.Forms.CarouselView)puede mostrar sus elementos verticalmente estableciendo [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) la propiedad en un `LinearItemsLayout` objeto, especificando el `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de la enumeración `Orientation` como el valor de la propiedad:

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

Esto da como resultado un diseño que crece verticalmente a medida que se agregan nuevos elementos:

[![Captura de pantalla de un diseño vertical de CarouselView, en iOS y Android](layout-images/vertical.png "CarouselView diseño vertical")](layout-images/vertical-large.png#lightbox "CarouselView diseño vertical")

## <a name="partially-visible-adjacent-items"></a>Elementos adyacentes parcialmente visibles

De forma predeterminada [`CarouselView`](xref:Xamarin.Forms.CarouselView) , muestra todos los elementos a la vez. Sin embargo, este comportamiento se puede cambiar estableciendo la `PeekAreaInsets` propiedad en un `Thickness` valor que especifica la cantidad de tiempo que pueden ver los elementos adyacentes parcialmente. Esto puede ser útil para indicar a los usuarios que hay elementos adicionales que ver. En el código XAML siguiente se muestra un ejemplo de cómo establecer esta propiedad:

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

El resultado es que los elementos adyacentes se exponen parcialmente en la pantalla:

[![Captura de pantalla de una CollectionView con elementos adyacentes parcialmente visibles, en iOS y Android](layout-images/peek-items.png "CarouselView indefinidos del área de inspección")](layout-images/peek-items-large.png#lightbox "Indefinidos de área máxima de CarouselView")

## <a name="item-spacing"></a>Espaciado de elementos

De forma predeterminada, no hay ningún espacio entre cada elemento de [`CarouselView`](xref:Xamarin.Forms.CarouselView). Este comportamiento se puede cambiar estableciendo la `ItemSpacing` propiedad en el diseño de los elementos que utiliza `CarouselView`.

Cuando un [`CarouselView`](xref:Xamarin.Forms.CarouselView) establece su [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propiedad en un `LinearItemsLayout` objeto, la `LinearItemsLayout.ItemSpacing` propiedad se puede establecer en un `double` valor que representa el espacio entre los elementos:

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
> La `LinearItemsLayout.ItemSpacing` propiedad tiene un conjunto de devoluciones de llamada de validación, que garantiza que el valor de la propiedad sea siempre mayor o igual que 0.

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

Este código genera un diseño vertical, que tiene un espaciado de 20 elementos.

## <a name="dynamic-resizing-of-items"></a>Cambio de tamaño dinámico de los elementos

Los elementos de [`CarouselView`](xref:Xamarin.Forms.CarouselView) un se pueden cambiar de tamaño dinámicamente en tiempo de ejecución cambiando las propiedades relacionadas con el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)diseño de los elementos dentro de. Por ejemplo, en el ejemplo de código siguiente [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) se [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) cambian las propiedades [`Image`](xref:Xamarin.Forms.Image) y de un objeto `HeightRequest` , y la propiedad [`Frame`](xref:Xamarin.Forms.Frame)de su elemento primario:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

El `OnImageTapped` controlador de eventos se ejecuta en respuesta a [`Image`](xref:Xamarin.Forms.Image) un objeto que se está punteando y cambia las dimensiones de la imagen ( `Frame`y su elemento primario), para que se vea más fácilmente:

[![Captura de pantalla de un CarouselView con el tamaño dinámico de los elementos, en iOS y Android](layout-images/runtime-resizing.png "Tamaño de elemento dinámico de CarouselView")](layout-images/runtime-resizing-large.png#lightbox "Tamaño de elemento dinámico de CarouselView")

## <a name="right-to-left-layout"></a>Diseño de derecha a izquierda

[`CarouselView`](xref:Xamarin.Forms.CarouselView)puede diseñar su contenido en una dirección de flujo de derecha a izquierda estableciendo su [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en. [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) Sin embargo, `FlowDirection` la propiedad debe establecerse idealmente en un diseño de página o raíz, lo que hace que todos los elementos de la página o el diseño raíz respondan a la dirección del flujo:

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

El valor [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predeterminado de un elemento con un elemento [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)primario es. Por consiguiente, [`CarouselView`](xref:Xamarin.Forms.CarouselView) hereda el `FlowDirection` valor de propiedad de [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Desplazamiento de CarouselView de Xamarin. Forms](scrolling.md)
