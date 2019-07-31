---
title: Estilos de dispositivo en Xamarin.Forms
description: Xamarin.Forms incluye seis estilos dinámicos, conocidos como estilos de dispositivo, en la clase Device.Styles. Este artículo explica cómo usar los estilos de dispositivo en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 4131844d49d7fdad4c97d07fb699b96db2020ec4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647292"
---
# <a name="device-styles-in-xamarinforms"></a>Estilos de dispositivo en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Xamarin.Forms incluye seis estilos dinámicos, conocidos como estilos de dispositivo, en la clase Device.Styles._

El *dispositivo* estilos son:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Solo pueden aplicarse a todos los seis estilos [ `Label` ](xref:Xamarin.Forms.Label) instancias. Por ejemplo, un `Label` que muestra el cuerpo de un párrafo puede establecer su [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedad [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle).

En el ejemplo de código siguiente se muestra cómo utilizar el *dispositivo* estilos en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Los estilos de dispositivo están enlazados a usar el `DynamicResource` extensión de marcado. La naturaleza dinámica de los estilos se puede ver en iOS cambiando el **accesibilidad** configuración para el tamaño del texto. La apariencia de la *dispositivo* estilos es diferente en cada plataforma, como se muestra en las capturas de pantalla siguiente:

![](device-images/device-styles.png "Estilos de dispositivo en cada plataforma")

*Dispositivo* estilos también se pueden derivar de estableciendo el [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad para el nombre de clave para el estilo del dispositivo. En el ejemplo de código anterior, `myBodyStyle` hereda [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle) y establece un color de texto acentuados. Para obtener más información sobre la herencia de estilo dinámico, consulte [herencia de estilo dinámico](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

El [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propiedad de cada uno [ `Label` ](xref:Xamarin.Forms.Label) instancia se establece en la propiedad adecuada de la [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) clase.

## <a name="accessibility"></a>Accesibilidad

El *dispositivo* estilos respetan las preferencias de accesibilidad, por lo que cambiarán los tamaños de fuente que se modifiquen las preferencias de accesibilidad en cada plataforma. Por lo tanto, para admitir texto accesible, asegúrese de que el *dispositivo* estilos se usan como base para los estilos de texto dentro de la aplicación.

Las capturas de pantalla siguientes muestran los estilos de dispositivo en cada plataforma, con el menor tamaño de fuente accesible:

[![](device-images/minimum-size.png "Estilos de dispositivo pequeño accesible en cada plataforma")](device-images/minimum-size-large.png#lightbox "estilos de dispositivo pequeño accesible en cada plataforma")

Las capturas de pantalla siguientes muestran los estilos de dispositivo en cada plataforma, con el mayor tamaño de fuente accesible:

![](device-images/maximum-size.png "Estilos de dispositivo accesible de gran tamaño en cada plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [Estilos de texto](~/xamarin-forms/user-interface/text/styles.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
