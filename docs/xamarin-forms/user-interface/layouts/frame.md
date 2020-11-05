---
title: Xamarin.Forms Grama
description: La Xamarin.Forms clase de marco es un diseño que se usa para ajustar una vista o un diseño con un borde que se puede configurar con color, sombra y otras opciones.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ba5fd2e8488f1f28f6bdc02b85c8e41fa212be32
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373749"
---
# <a name="no-locxamarinforms-frame"></a>Xamarin.Forms Grama

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

La Xamarin.Forms [`Frame`](xref:Xamarin.Forms.Frame) clase es un diseño que se usa para ajustar una vista con un borde que se puede configurar con color, sombra y otras opciones. Los marcos se suelen usar para crear bordes alrededor de los controles, pero se pueden usar para crear una interfaz de usuario más compleja. Para obtener más información, consulte [uso avanzado de fotogramas](#advanced-frame-usage).

En la captura de pantalla siguiente se muestran `Frame` los controles de iOS y Android:

[!["Ejemplos de marcos en iOS y Android"](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "Ejemplos de fotogramas en iOS y Android")

La clase `Frame` define las propiedades siguientes:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) es un `Color` valor que determina el color del `Frame` borde.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) es un `float` valor que determina el radio redondeado de la esquina.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) es un `bool` valor que determina si el marco tiene una sombra paralela.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que `Frame` puede ser el destino de los enlaces de datos.

> [!NOTE]
> El `HasShadow` comportamiento de la propiedad depende de la plataforma. El valor predeterminado es `true` en todas las plataformas. Sin embargo, en las sombras paralelas de UWP no se representan. Las sombras paralelas se representan en Android y en iOS, pero las sombras paralelas en iOS son más oscuras y ocupan más espacio.

## <a name="create-a-frame"></a>Crear un marco

`Frame`Se puede crear una instancia de en XAML. El `Frame` objeto predeterminado tiene un fondo blanco, una sombra paralela y ningún borde. Un `Frame` objeto normalmente ajusta otro control. En el ejemplo siguiente se muestra un `Frame` contenedor predeterminado de un `Label` objeto:

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

`Frame`También se puede crear en el código:

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame` los objetos se pueden personalizar con esquinas redondeadas, bordes coloreados y sombras paralelas estableciendo propiedades en el código XAML. En el ejemplo siguiente se muestra un `Frame` objeto personalizado:

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

Estas propiedades de instancia también se pueden establecer en el código:

```csharp
Frame frame = new Frame
{
    BorderColor = Color.Orange,
    CornerRadius = 10,
    HasShadow = true,
    Content = new Label { Text = "Example" }
};
```

## <a name="advanced-frame-usage"></a>Uso avanzado de fotogramas

La `Frame` clase hereda de `ContentView` , lo que significa que puede contener cualquier tipo de `View` objeto, incluidos los `Layout` objetos. Esta capacidad permite que `Frame` se utilice para crear objetos de interfaz de usuario complejos, como tarjetas.

### <a name="create-a-card-with-a-frame"></a>Crear una tarjeta con un marco

La combinación `Frame` de un objeto con un `Layout` objeto como un `StackLayout` objeto permite la creación de interfaces de usuario más complejas. La siguiente captura de pantalla muestra una tarjeta de ejemplo, creada con un `Frame` objeto:

[!["Captura de pantalla de una tarjeta creada con un fotograma"](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "Captura de pantalla de una tarjeta creada con un marco")

En el siguiente código XAML se muestra cómo crear una tarjeta con la `Frame` clase:

```xaml
<Frame BorderColor="Gray"
       CornerRadius="5"
       Padding="8">
  <StackLayout>
    <Label Text="Card Example"
           FontSize="Medium"
           FontAttributes="Bold" />
    <BoxView Color="Gray"
             HeightRequest="2"
             HorizontalOptions="Fill" />
    <Label Text="Frames can wrap more complex layouts to create more complex UI components, such as this card!"/>
  </StackLayout>
</Frame>
```

También se puede crear una tarjeta en el código:

```csharp
Frame cardFrame = new Frame
{
    BorderColor = Color.Gray,
    CornerRadius = 5,
    Padding = 8,
    Content = new StackLayout
    {
        Children =
        {
            new Label
            {
                Text = "Card Example",
                FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label)),
                FontAttributes = FontAttributes.Bold
            },
            new BoxView
            {
                Color = Color.Gray,
                HeightRequest = 2,
                HorizontalOptions = LayoutOptions.Fill
            },
            new Label
            {
                Text = "Frames can wrap more complex layouts to create more complex UI components, such as this card!"
            }
        }
    }
};
```

### <a name="round-elements"></a>Elementos Round

La `CornerRadius` propiedad del `Frame` control se puede utilizar para crear una imagen de círculo. En la captura de pantalla siguiente se muestra un ejemplo de una imagen redondeada creada mediante un `Frame` objeto:

[!["Captura de pantalla de una imagen de círculo creada con un fotograma"](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Captura de pantalla de una imagen de círculo creada con un marco")

En el código XAML siguiente se muestra cómo crear una imagen de círculo en XAML:

```xaml
<Frame Margin="10"
       BorderColor="Black"
       CornerRadius="50"
       HeightRequest="60"
       WidthRequest="60"
       IsClippedToBounds="True"
       HorizontalOptions="Center"
       VerticalOptions="Center">
  <Image Source="outdoors.jpg"
         Aspect="AspectFill"
         Margin="-20"
         HeightRequest="100"
         WidthRequest="100" />
</Frame>
```

También se puede crear una imagen circular en el código:

```csharp
Frame circleImageFrame = new Frame
{
    Margin = 10,
    BorderColor = Color.Black,
    CornerRadius = 50,
    HeightRequest = 60,
    WidthRequest = 60,
    IsClippedToBounds = true,
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center,
    Content = new Image
    {
        Source = ImageSource.FromFile("outdoors.jpg"),
        Aspect = Aspect.AspectFill,
        Margin = -20,
        HeightRequest = 100,
        WidthRequest = 100
    }
};
```

La imagen de **outdoors.jpg** se debe agregar a cada proyecto de plataforma y la manera en que se logra esto varía según la plataforma. Para obtener más información, vea [imágenes Xamarin.Forms en ](~/xamarin-forms/user-interface/images.md).

> [!NOTE]
> Las esquinas redondeadas se comportan de manera ligeramente diferente en las distintas plataformas. El `Image` objeto `Margin` debe ser la mitad de la diferencia entre el ancho de la imagen y el ancho del marco primario, y debe ser negativo para centrar la imagen uniformemente dentro del `Frame` objeto. Sin embargo, no se garantiza el ancho y el alto solicitados, por lo que `Margin` `HeightRequest` es posible que sea necesario modificar las propiedades, y en `WidthRequest` función del tamaño de la imagen y otras opciones de diseño.

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de fotogramas](/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Imágenes en Xamarin.Forms](~/xamarin-forms/user-interface/images.md)