---
title: Xamarin.Forms ImageButton
description: El ImageButton muestra una imagen y responde a un pulse o haga clic en que se dirige a una aplicación para llevar a cabo una tarea determinada.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 90d860cb6429025672a058e22e76f029872d3b34
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657174"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_El ImageButton muestra una imagen y responde a un pulse o haga clic en que se dirige a una aplicación para llevar a cabo una tarea determinada._

El `ImageButton` ver combina el [ `Button` ](xref:Xamarin.Forms.Button) vista y [ `Image` ](xref:Xamarin.Forms.Image) vista para crear un botón cuyo contenido es una imagen. El usuario presiona el `ImageButton` con un dedo o hace clic en él con el mouse para dirigir la aplicación para llevar a cabo una tarea determinada. Sin embargo, a diferencia del `Button` vista, la `ImageButton` vista no tiene ningún concepto de texto y la apariencia del texto.

> [!NOTE]
> Mientras el [ `Button` ](xref:Xamarin.Forms.Button) vista define un [ `Image` ](xref:Xamarin.Forms.Button.Image) propiedad, que permite mostrar una imagen en el `Button`, esta propiedad está pensada para utilizarse cuando se muestra un icono pequeño junto a la `Button` texto.

Los ejemplos de código en esta guía se toman de la [FormsGallery ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Establecer el origen de imagen

`ImageButton` define un `Source` propiedad que se debe establecer en la imagen para mostrar en el botón, con el origen de la imagen que se va a un archivo, un URI, una secuencia o un recurso. Para obtener más información acerca de cómo cargar imágenes de orígenes diferentes, consulte [imágenes en Xamarin.Forms](images.md).

El ejemplo siguiente muestra cómo crear una instancia de un `ImageButton` en XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El `Source` propiedad especifica la imagen que aparece en el `ImageButton`. En este ejemplo se establece en un archivo local que se cargarán de cada proyecto de plataforma, lo que resulta en las capturas de pantalla siguiente:

[![ImageButton básica](imagebutton-images/BasicImageButton.png "ImageButton básica")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton básica")

De forma predeterminada, el `ImageButton` es rectangular, pero puede dar TI redondeada esquinas mediante el `CornerRadius` propiedad. Para obtener más información acerca de `ImageButton` apariencia, consulte [ImageButton apariencia](#imagebutton-appearance).

El ejemplo siguiente muestra cómo crear una página que es funcionalmente equivalente al ejemplo XAML anterior, pero en su totalidad C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>Control ImageButton hace clic en

`ImageButton` define un `Clicked` evento que se desencadena cuando el usuario pulsa el `ImageButton` con un puntero dedo o el mouse. El evento se desencadena cuando se suelta el botón dedo o el mouse de la superficie de la `ImageButton`. El `ImageButton` debe tener su `IsEnabled` propiedad establecida en `true` para responder a las pulsaciones.

El ejemplo siguiente muestra cómo crear una instancia de un `ImageButton` en XAML y controle su `Clicked` eventos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El `Clicked` evento está establecido en un controlador de eventos denominado `OnImageButtonClicked` que se encuentra en el archivo de código subyacente:

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

Cuando el `ImageButton` se pulsa, el `OnImageButtonClicked` método se ejecuta. El `sender` argumento es el `ImageButton` responsable de este evento. Se puede usar para tener acceso a la `ImageButton` objeto, o para distinguir entre varias `ImageButton` objetos que comparten el mismo `Clicked` eventos.

Esta particular `Clicked` controlador incrementa un contador y muestra el valor del contador en un [ `Label` ](xref:Xamarin.Forms.Label):

[![Haga clic en básico de ImageButton](imagebutton-images/ImageButton.png "básica clic ImageButton")](imagebutton-images/ImageButton-Large.png#lightbox "básica clic ImageButton")

El ejemplo siguiente muestra cómo crear una página que es funcionalmente equivalente al ejemplo XAML anterior, pero en su totalidad C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>Deshabilitar el ImageButton

A veces, una aplicación está en un estado determinado donde un determinado `ImageButton` haga clic en no es una operación válida. En esos casos, el `ImageButton` debe deshabilitarse estableciendo su `IsEnabled` propiedad `false`.

## <a name="using-the-command-interface"></a>Mediante la interfaz de comandos

Es posible que una aplicación responda a `ImageButton` derivaciones sin control el `Clicked` eventos. El `ImageButton` implementa un mecanismo de notificación alternativo denominado el _comando_ o _comandos_ interfaz. Consta de dos propiedades:

- `Command` de tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), una interfaz definida en el [ `System.Windows.Input` ](xref:System.Windows.Input) espacio de nombres.
- `CommandParameter` propiedad de tipo [ `Object` ](xref:System.Object).

Este enfoque es adecuado en relación con el enlace de datos y especialmente al implementar la arquitectura Model-View-ViewModel (MVVM).

Para obtener más información sobre el uso de la interfaz de comandos, consulte [mediante la interfaz de comandos](button.md#using-the-command-interface) en el [botón](button.md) guía.

## <a name="pressing-and-releasing-the-imagebutton"></a>Presionando y soltando el ImageButton

Además el `Clicked` eventos, `ImageButton` también define `Pressed` y `Released` eventos. El `Pressed` evento tiene lugar cuando se presiona un dedo en un `ImageButton`, o se presiona un botón del mouse con el puntero situado sobre el `ImageButton`. El `Released` evento tiene lugar cuando se suelta el botón del dedo o el mouse. Por lo general, el `Clicked` también se desencadena el evento al mismo tiempo que el `Released` evento, pero si el puntero del dedo o el mouse se desliza fuera de la superficie de la `ImageButton` antes de que se publique el `Clicked` eventos podrían no producirse.

Para obtener más información acerca de estos eventos, vea [presionando y soltando el botón](button.md#pressing-and-releasing-the-button) en el [botón](button.md) guía.

## <a name="imagebutton-appearance"></a>Apariencia ImageButton

Además de las propiedades que `ImageButton` hereda el [ `View` ](xref:Xamarin.Forms.View) (clase), `ImageButton` también define varias propiedades que afectan a su apariencia:

- `Aspect` es cómo la imagen se ajustarán el área de presentación.
- `BorderColor` es el color de un área que rodea el `ImageButton`.
- `BorderWidth` es el ancho del borde.
- `CornerRadius` es el radio de redondeo de la `ImageButton`.

El `Aspect` propiedad puede establecerse en uno de los miembros de la [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeración:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -ajusta la imagen para rellenar por completo y exactamente el `ImageButton`. Esto puede dar lugar a que la imagen se distorsiona.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -recorta la imagen para que rellene el `ImageButton` conservando la relación de aspecto.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterbox la imagen (si es necesario) para que quepa la imagen completa en el `ImageButton`, con espacio en blanco que se agrega a la parte superior o inferior o lados dependiendo de si la imagen es ancho o alto. Este es el valor predeterminado de la [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeración.

> [!NOTE]
> El `ImageButton` clase también tiene [ `Margin` ](xref:Xamarin.Forms.View.Margin) y `Padding` las propiedades que controlan el comportamiento de diseño de la `ImageButton`. Para obtener más información, consulte [margen y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Estados visuales ImageButton

`ImageButton` tiene un `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que puede utilizarse para iniciar un cambio visual en el `ImageButton` cuando presiona por el usuario, siempre que lo esté habilitada.

En el siguiente ejemplo XAML se muestra cómo definir un estado visual para el `Pressed` estado:

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

El `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) especifica que, cuando el `ImageButton` está presionado, su [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) se cambiará la propiedad de su valor predeterminado de 1 a 0,8. El `Normal` `VisualState` especifica que, cuando el `ImageButton` está en estado normal, su `Scale` propiedad se establecerá en 1. Por lo tanto, el efecto general es que cuando el `ImageButton` está presionado, se vuelve a escalar para que sea ligeramente más pequeñas pero cuando el `ImageButton` está publicado, se escalan a su tamaño predeterminado.

Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
