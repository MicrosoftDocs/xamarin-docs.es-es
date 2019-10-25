---
title: Marco de Xamarin. Forms
description: La clase de marco de Xamarin. Forms es un diseño que se usa para ajustar una vista o un diseño con un borde que se puede configurar con color, sombra y otras opciones.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
ms.openlocfilehash: 619b29a9d65594b1badd805c3361fe1a174d7174
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2019
ms.locfileid: "69976494"
---
# <a name="xamarinforms-frame"></a>Marco de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

La clase de [`Frame`](xref:Xamarin.Forms.Frame) de Xamarin. Forms es un diseño que se usa para ajustar una vista con un borde que se puede configurar con color, sombra y otras opciones. Los marcos se suelen usar para crear bordes alrededor de los controles, pero se pueden usar para crear una interfaz de usuario más compleja. Para obtener más información, consulte [uso avanzado de fotogramas](#advanced-frame-usage).

En la captura de pantalla siguiente se muestra `Frame` controles en iOS y Android:

[![ejemplos de marcos en iOS y Android](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "Ejemplos de fotogramas en iOS y Android")

La clase `Frame` define las siguientes propiedades:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) es un valor `Color` que determina el color del borde `Frame`.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) es un valor `float` que determina el radio redondeado de la esquina.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) es un valor `bool` que determina si el marco tiene una sombra paralela.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que el `Frame` puede ser el destino de los enlaces de datos.

> [!NOTE]
> El comportamiento de la propiedad `HasShadow` depende de la plataforma. El valor predeterminado es `true` en todas las plataformas. Sin embargo, en las sombras paralelas de UWP no se representan. Las sombras paralelas se representan en Android y en iOS, pero las sombras paralelas en iOS son más oscuras y ocupan más espacio.

## <a name="create-a-frame"></a>Crear un marco

Se puede crear una instancia de `Frame` en XAML. El objeto `Frame` predeterminado tiene un fondo blanco, una sombra paralela y ningún borde. Un objeto `Frame` normalmente ajusta otro control. En el ejemplo siguiente se muestra un `Frame` predeterminado que ajusta un objeto `Label`:

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

También se puede crear un `Frame` en el código:

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame` objetos se pueden personalizar con esquinas redondeadas, bordes coloreados y sombras paralelas mediante el establecimiento de propiedades en el código XAML. En el ejemplo siguiente se muestra un objeto de `Frame` personalizado:

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

La clase `Frame` hereda de `ContentView`, lo que significa que puede contener cualquier tipo de `View` objeto, incluidos los objetos `Layout`. Esta capacidad permite usar el `Frame` para crear objetos de interfaz de usuario complejos, como tarjetas.

### <a name="create-a-card-with-a-frame"></a>Crear una tarjeta con un marco

La combinación de un objeto `Frame` con un objeto `Layout` como un objeto `StackLayout` permite la creación de interfaces de usuario más complejas. La siguiente captura de pantalla muestra una tarjeta de ejemplo, creada con un objeto `Frame`:

[![captura de pantalla de una tarjeta creada con un fotograma](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "Captura de pantalla de una tarjeta creada con un marco")

En el siguiente código XAML se muestra cómo crear una tarjeta con la clase `Frame`:

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

La propiedad `CornerRadius` del control `Frame` se puede utilizar para crear una imagen de círculo. En la captura de pantalla siguiente se muestra un ejemplo de una imagen redonda, creada con un objeto `Frame`:

[![ "captura de pantalla de una imagen de círculo creada con un fotograma"](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Captura de pantalla de una imagen de círculo creada con un marco")

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

La imagen del **exterior. jpg** debe agregarse a cada proyecto de la plataforma y la manera en que se logra esto varía según la plataforma. Para obtener más información, vea [imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

> [!NOTE]
> Las esquinas redondeadas se comportan de manera ligeramente diferente en las distintas plataformas. El `Margin` del objeto `Image` debe ser la mitad de la diferencia entre el ancho de la imagen y el ancho del marco primario, y debe ser negativo para centrar la imagen uniformemente dentro del objeto `Frame`. Sin embargo, no se garantiza el ancho y el alto solicitados, por lo que es posible que sea necesario modificar las propiedades `Margin`, `HeightRequest` y `WidthRequest` en función del tamaño de la imagen y de otras opciones de diseño.

## <a name="related-links"></a>Vínculos relacionados

* [Demostraciones de fotogramas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Imágenes de Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
