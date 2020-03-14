---
title: Xamarin.Forms ImageButton
description: El ImageButton muestra una imagen y responde a un pulse o haga clic en que se dirige a una aplicación para llevar a cabo una tarea determinada.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 7c6647a0299b5ece3caaaa1d322ec1a0efac3557
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305718"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_El ImageButton muestra una imagen y responde a una derivación o a un clic que dirige una aplicación para llevar a cabo una tarea determinada._

La vista `ImageButton` combina la vista [`Button`](xref:Xamarin.Forms.Button) y la vista [`Image`](xref:Xamarin.Forms.Image) para crear un botón cuyo contenido es una imagen. El usuario presiona el `ImageButton` con un dedo o hace clic en él con el mouse para indicar a la aplicación que lleve a cabo una tarea determinada. Sin embargo, a diferencia de la vista de `Button`, la vista de `ImageButton` no tiene ningún concepto de aspecto de texto y texto.

> [!NOTE]
> Mientras que la vista [`Button`](xref:Xamarin.Forms.Button) define una propiedad [`Image`](xref:Xamarin.Forms.Button.Image) , que permite mostrar una imagen en el `Button`, esta propiedad está pensada para usarse al mostrar un icono pequeño junto al texto `Button`.

Los ejemplos de código de esta guía se toman del [ejemplo FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Establecer el origen de imagen

`ImageButton` define una propiedad `Source` que debe establecerse en la imagen que se va a mostrar en el botón, con el origen de la imagen como un archivo, un URI, un recurso o un flujo. Para obtener más información sobre cómo cargar imágenes de distintos orígenes, vea [imágenes en Xamarin. Forms](images.md).

En el ejemplo siguiente se muestra cómo crear una instancia de un `ImageButton` en XAML:

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

La propiedad `Source` especifica la imagen que aparece en el `ImageButton`. En este ejemplo se establece en un archivo local que se cargarán de cada proyecto de plataforma, lo que resulta en las capturas de pantalla siguiente:

[![ImageButton básico](imagebutton-images/BasicImageButton.png "ImageButton básico")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton básico")

De forma predeterminada, el `ImageButton` es rectangular, pero puede asignarle esquinas redondeadas mediante el uso de la propiedad `CornerRadius`. Para obtener más información sobre la apariencia de `ImageButton`, vea la [apariencia de ImageButton](#imagebutton-appearance).

> [!NOTE]
> Aunque un `ImageButton` puede cargar un GIF animado, solo mostrará el primer fotograma del GIF.

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

`ImageButton` define un evento de `Clicked` que se desencadena cuando el usuario puntea el `ImageButton` con un dedo o un puntero del mouse. El evento se desencadena cuando el dedo o el botón del mouse se suelta de la superficie del `ImageButton`. El `ImageButton` debe tener su propiedad `IsEnabled` establecida en `true` para responder a los grifos.

En el ejemplo siguiente se muestra cómo crear una instancia de un `ImageButton` en XAML y administrar su evento `Clicked`:

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

El evento `Clicked` se establece en un controlador de eventos denominado `OnImageButtonClicked` que se encuentra en el archivo de código subyacente:

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

Cuando se puntea el `ImageButton`, se ejecuta el método `OnImageButtonClicked`. El `sender` argumento es el `ImageButton` responsable de este evento. Puede utilizar esto para tener acceso al objeto `ImageButton` o para distinguir entre varios objetos `ImageButton` que comparten el mismo evento `Clicked`.

Este controlador de `Clicked` concreto incrementa un contador y muestra el valor del contador en un [`Label`](xref:Xamarin.Forms.Label):

[![Clic en ImageButton básico](imagebutton-images/ImageButton.png "Clic en ImageButton básico")](imagebutton-images/ImageButton-Large.png#lightbox "Clic en ImageButton básico")

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

A veces, una aplicación se encuentra en un estado determinado en el que una determinada `ImageButton` clic no es una operación válida. En estos casos, el `ImageButton` debe deshabilitarse estableciendo su propiedad `IsEnabled` en `false`.

## <a name="using-the-command-interface"></a>Mediante la interfaz de comandos

Es posible que una aplicación responda a las pulsaciones de `ImageButton` sin controlar el evento de `Clicked`. El `ImageButton` implementa un mecanismo de notificación alternativo denominado _comando_ o interfaz de _comandos_ . Consta de dos propiedades:

- `Command` de tipo [`ICommand`](xref:System.Windows.Input.ICommand), una interfaz definida en el espacio de nombres [`System.Windows.Input`](xref:System.Windows.Input) .
- `CommandParameter` propiedad de tipo [`Object`](xref:System.Object).

Este enfoque es adecuado en relación con el enlace de datos y especialmente al implementar la arquitectura Model-View-ViewModel (MVVM).

Para obtener más información acerca del uso de la interfaz de comandos, consulte [uso de la interfaz de comandos](button.md#using-the-command-interface) en la guía de [botones](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Presionando y soltando el ImageButton

Además del evento `Clicked`, `ImageButton` también define los eventos `Pressed` y `Released`. El evento `Pressed` se produce cuando se presiona un dedo en un `ImageButton`o cuando se presiona un botón del mouse con el puntero situado sobre la `ImageButton`. El evento `Released` tiene lugar cuando se suelta el dedo o el botón del mouse. Por lo general, el evento de `Clicked` también se desencadena al mismo tiempo que el evento de `Released`, pero si el puntero del dedo o del mouse se desplaza fuera de la superficie de la `ImageButton` antes de su lanzamiento, el evento de `Clicked` podría no producirse.

Para obtener más información sobre estos eventos, vea [presionar y soltar el botón](button.md#pressing-and-releasing-the-button) en la guía de [botones](button.md) .

## <a name="imagebutton-appearance"></a>Apariencia ImageButton

Además de las propiedades que `ImageButton` heredan de la clase [`View`](xref:Xamarin.Forms.View) , `ImageButton` también define varias propiedades que afectan a su apariencia:

- `Aspect` es cómo se escalará la imagen para ajustarse al área de visualización.
- `BorderColor` es el color de un área alrededor de la `ImageButton`.
- `BorderWidth` es el ancho del borde.
- `CornerRadius` es el radio de la esquina del `ImageButton`.

La propiedad `Aspect` se puede establecer en uno de los miembros de la enumeración [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) : estira la imagen para rellenar el `ImageButton`de forma completa y exacta. Esto puede dar lugar a que la imagen se distorsiona.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) : recorta la imagen para que rellene el `ImageButton` mientras conserva la relación de aspecto.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) : panorámica la imagen (si es necesario) para que toda la imagen quepa en el `ImageButton`, con un espacio en blanco agregado a la parte superior o inferior, en función de si la imagen es ancha o alta. Este es el valor predeterminado de la enumeración [`Aspect`](xref:Xamarin.Forms.Aspect) .

> [!NOTE]
> La clase `ImageButton` también tiene [`Margin`](xref:Xamarin.Forms.View.Margin) y `Padding` propiedades que controlan el comportamiento del diseño del `ImageButton`. Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Estados visuales ImageButton

`ImageButton` tiene un [`VisualState`](xref:Xamarin.Forms.VisualState) de `Pressed` que se puede usar para iniciar un cambio visual en el `ImageButton` cuando lo presiona el usuario, siempre que esté habilitado.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual para el estado `Pressed`:

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

El [`VisualState`](xref:Xamarin.Forms.VisualState) `Pressed` especifica que cuando se presiona el `ImageButton`, su propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) se cambiará de su valor predeterminado de 1 a 0,8. El `VisualState` `Normal` especifica que cuando el `ImageButton` se encuentra en un estado normal, su propiedad `Scale` se establecerá en 1. Por lo tanto, el efecto general es que, cuando se presiona el `ImageButton`, se vuelve a escalar para que sea ligeramente menor y, cuando se libera el `ImageButton`, se vuelve a escalar a su tamaño predeterminado.

Para obtener más información sobre los Estados visuales, vea [el administrador de estado visual de Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
