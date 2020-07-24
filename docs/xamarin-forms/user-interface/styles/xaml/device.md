---
title: Estilos de dispositivo enXamarin.Forms
description: Xamarin.Formsincluye seis estilos dinámicos, conocidos como estilos de dispositivo, en la clase Device. Styles. En este artículo se explica cómo consumir los estilos de dispositivo en una Xamarin.Forms aplicación.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 04669479bb321da4fee6c45fd0f2c00deb5bbf1a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929732"
---
# <a name="device-styles-in-xamarinforms"></a>Estilos de dispositivo enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Xamarin.Formsincluye seis estilos dinámicos, conocidos como estilos de dispositivo, en la clase Device. Styles._

Los estilos de *dispositivo* son los siguientes:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Los seis estilos solo se pueden aplicar a [`Label`](xref:Xamarin.Forms.Label) las instancias de. Por ejemplo, un `Label` que muestra el cuerpo de un párrafo podría establecer su [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedad en [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) .

En el ejemplo de código siguiente se muestra cómo usar los estilos de *dispositivo* en una página XAML:

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

Los estilos del dispositivo se enlazan al uso de la `DynamicResource` extensión de marcado. La naturaleza dinámica de los estilos puede verse en iOS cambiando la configuración de **accesibilidad** para el tamaño del texto. La apariencia de los estilos de *dispositivo* es diferente en cada plataforma, como se muestra en las siguientes capturas de pantallas:

![Estilos de dispositivo en cada plataforma](device-images/device-styles.png)

Los estilos de *dispositivo* también se pueden derivar mediante el establecimiento de la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propiedad en el nombre de clave para el estilo de dispositivo. En el ejemplo de código anterior, `myBodyStyle` hereda de [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) y establece un color de texto acentuado. Para obtener más información sobre la herencia de estilo dinámico, consulte [herencia de estilo dinámico](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

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

La [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propiedad de cada [`Label`](xref:Xamarin.Forms.Label) instancia se establece en la propiedad adecuada de la [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) clase.

## <a name="accessibility"></a>Accesibilidad

Los estilos de *dispositivo* respetan las preferencias de accesibilidad, por lo que los tamaños de fuente cambian a medida que se modifican las preferencias de accesibilidad en cada plataforma. Por lo tanto, para admitir texto accesible, asegúrese de que los estilos de *dispositivo* se usan como base para cualquier estilo de texto dentro de la aplicación.

Las capturas de pantallas siguientes muestran los estilos de dispositivo en cada plataforma, con el tamaño de fuente accesible más pequeño:

[![Estilos de dispositivo pequeño accesible en cada plataforma](device-images/minimum-size.png)](device-images/minimum-size-large.png#lightbox "Estilos de dispositivo pequeño accesible en cada plataforma")

Las capturas de pantallas siguientes muestran los estilos de dispositivo en cada plataforma, con el tamaño de fuente accesible más grande:

![Estilos de dispositivo grande accesible en cada plataforma](device-images/maximum-size.png)

## <a name="related-links"></a>Vínculos relacionados

- [Estilos de texto](~/xamarin-forms/user-interface/text/styles.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Trabajar con estilos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device. Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
