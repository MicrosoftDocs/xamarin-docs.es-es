---
title: Xamarin.Forms StackLayout
description: En este artículo se explica cómo usar la clase Xamarin.Forms StackLayout para presentar las colecciones de vistas a través de una dimensión.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 28a4fc6527a8c6990c47cdbbdc3163fc9350db83
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770276"
---
# <a name="xamarinforms-stacklayout"></a>Xamarin.Forms StackLayout

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[StackLayout](xref:Xamarin.Forms.StackLayout) organiza las vistas en una línea unidimensional ("stack"), ya sea horizontal o verticalmente. Las vistas en un `StackLayout` puede ajustarse según el espacio en el diseño mediante las opciones de diseño. Posición viene determinada por el orden en que se agregaron las vistas en el diseño y las opciones de diseño de las vistas.

[![](stack-layout-images/layouts-sml.png "Los diseños de Xamarin.Forms")](stack-layout-images/layouts.png#lightbox "los diseños de Xamarin.Forms")

## <a name="purpose"></a>Propósito

`StackLayout` es menos complejo que otras vistas. Se pueden crear interfaces lineales simple con solo agregar vistas a un `StackLayout`y las interfaces más complejas creadas por anidarlos.

## <a name="usage--behavior"></a>Uso de & comportamiento

### <a name="spacing"></a>Espaciado

De forma predeterminada, `StackLayout` agregará un margen 6px entre las vistas. Puede ser controlado o establecer tener ningún margen estableciendo el `Spacing` propiedad StackLayout. La siguiente muestra cómo establecer el espaciado y el efecto de las opciones de espaciado diferente:

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout Spacing="10" x:Name="layout">
      <Button Text="StackLayout" VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView Color="Yellow" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView Color="Green" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" Color="Blue" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { Text = "StackLayout", VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var yellowBox = new BoxView { Color = Color.Yellow, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var greenBox = new BoxView { Color = Color.Green, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var blueBox = new BoxView { Color = Color.Blue, VerticalOptions = LayoutOptions.FillAndExpand,
      HorizontalOptions = LayoutOptions.FillAndExpand, HeightRequest = 75 };

    layout.Children.Add(button);
    layout.Children.Add(yellowBox);
    layout.Children.Add(greenBox);
    layout.Children.Add(blueBox);
    layout.Spacing = 10;
    Content = layout;
  }
}
```

Espaciado = 0:

![](stack-layout-images/spacing-zero.png "StackLayout con espaciado = 0")

Espaciado de diez:

![](stack-layout-images/spacing-ten.png "StackLayout con espaciado = 10")

### <a name="sizing"></a>Ajuste de tamaño

El tamaño de una vista en un StackLayout depende de las solicitudes de alto y ancho y las opciones de diseño. `StackLayout` aplicará el relleno. La siguiente `LayoutOption`s hará que las vistas a ocupar espacio está disponible en el diseño:

- **CenterAndExpand** &ndash; centra la vista dentro del diseño y se expande para ocupar espacio le asignará el diseño.
- **EndAndExpand** &ndash; coloca la vista al final del diseño (inferior o límite más a la derecha) y se expande para ocupar espacio le asignará el diseño.
- **FillAndExpand** &ndash; coloca la vista para que no tiene relleno y ocupa espacio le asignará el diseño.
- **StartAndExpand** &ndash; coloca la vista al principio del diseño y ocupa espacio proporcionará el elemento primario.

Para obtener más información, consulte [expansión](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion).

### <a name="positioning"></a>Posición

Se pueden colocar y tamaño utilizando vistas en un StackLayout `LayoutOptions`. Cada vista puede proporcionarse `VerticalOptions` y `HorizontalOptions`, define cómo las vistas se posicionan en relación con el diseño. Siguiente predefinidos `LayoutOptions` están disponibles:

- **Centro de** &ndash; centra la vista dentro del diseño.
- **End** &ndash; coloca la vista al final del diseño (inferior o límite más a la derecha).
- **Rellenar** &ndash; coloca la vista para que no tenga ningún relleno.
- **Iniciar** &ndash; coloca la vista al principio del diseño.

El código siguiente muestra cómo establecer las opciones de diseño:

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout x:Name="layout">
      <Button VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var oneBox = new BoxView { VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var twoBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var threeBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };

    layout.Children.Add(button);
    layout.Children.Add(oneBox);
    layout.Children.Add(twoBox);
    layout.Children.Add(threeBox);
    Content = layout;
  }
}
```

Para obtener más información, consulte [alineación](~/xamarin-forms/user-interface/layouts/layout-options.md#alignment).

## <a name="exploring-a-complex-layout"></a>Exploración de un diseño complejo

Cada uno de los diseños tienen ventajas y desventajas para crear diseños determinados. A lo largo de esta serie de artículos de diseño, una aplicación de ejemplo creada con el mismo diseño de página que se implementa mediante tres diseños diferentes.

Tenga en cuenta el XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.StackLayoutPage"
BackgroundColor="Maroon"
Title="StackLayouts">
    <ContentPage.Content>
    <ScrollView>
      <StackLayout Spacing="0" Padding="0" BackgroundColor="Maroon">
        <BoxView HorizontalOptions="FillAndExpand" HeightRequest="100"
          VerticalOptions="Start" Color="Gray" />
        <Button BorderRadius="30" HeightRequest="60" WidthRequest="60"
          BackgroundColor="Red" HorizontalOptions="Center" VerticalOptions="Start" />
        <StackLayout HeightRequest="100" VerticalOptions="Start" HorizontalOptions="FillAndExpand" Spacing="20" BackgroundColor="Maroon">
          <Label Text="User Name" FontSize="28" HorizontalOptions="Center"
            VerticalOptions="Center" FontAttributes="Bold" />
          <Entry Text="Bio + Hashtags" TextColor="White"
            BackgroundColor="Maroon" HorizontalOptions="FillAndExpand" VerticalOptions="CenterAndExpand" />
        </StackLayout>
        <StackLayout Orientation="Horizontal" HeightRequest="50" BackgroundColor="White" Padding="5">
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="Start">
            <BoxView BackgroundColor="Black" WidthRequest="40" HeightRequest="40"  HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="EndAndExpand">
            <BoxView BackgroundColor="Maroon" WidthRequest="40" HeightRequest="40" HorizontalOptions="Start" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Age:" TextColor="White" HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="35" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Interests:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100"/>
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin, C#, .NET, Mono..." TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
      </StackLayout>
    </ScrollView>
    </ContentPage.Content>
</ContentPage>

```

El código anterior da como resultado el siguiente diseño:

![](stack-layout-images/stack.png "StackLayout complejo")

Observe que están `StackLayouts` anidados porque, en algunos casos, el anidamiento de diseños puede ser más fácil que presentar todos los elementos dentro del mismo diseño. Observe también que, dado que `StackLayout` no admite elementos superpuestos, la página no dispone de algunos de los detalles de diseño se encuentra en las páginas de los otros diseños.

## <a name="related-links"></a>Vínculos relacionados

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
