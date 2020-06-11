---
title: " Xamarin.Forms ImageButton" Description: "el ImageButton muestra una imagen y responde a una derivación o a un clic que dirige a una aplicación para llevar a cabo una tarea determinada".
MS. Prod: Xamarin ms. AssetID: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/04/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-imagebutton"></a>Xamarin.FormsImageButton

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_El ImageButton muestra una imagen y responde a una derivación o a un clic que dirige una aplicación para llevar a cabo una tarea determinada._

La `ImageButton` vista combina la [`Button`](xref:Xamarin.Forms.Button) vista y la [`Image`](xref:Xamarin.Forms.Image) vista para crear un botón cuyo contenido es una imagen. El usuario presiona el `ImageButton` con un dedo o hace clic en él con el mouse para indicar a la aplicación que lleve a cabo una tarea determinada. Sin embargo, a diferencia `Button` de la vista, la `ImageButton` vista no tiene ningún concepto de aspecto de texto y texto.

> [!NOTE]
> Mientras [`Button`](xref:Xamarin.Forms.Button) que la vista define una [`Image`](xref:Xamarin.Forms.Button.Image) propiedad, que permite mostrar una imagen en `Button` , esta propiedad está pensada para usarse al mostrar un icono pequeño junto al `Button` texto.

Los ejemplos de código de esta guía se toman del [ejemplo FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Establecer el origen de la imagen

`ImageButton`define una `Source` propiedad que debe establecerse en la imagen que se va a mostrar en el botón, con el origen de la imagen como un archivo, un URI, un recurso o un flujo. Para obtener más información sobre cómo cargar imágenes de orígenes diferentes, vea [imágenes en Xamarin.Forms ](images.md).

En el ejemplo siguiente se muestra cómo crear una instancia `ImageButton` de en XAML:

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

La `Source` propiedad especifica la imagen que aparece en el `ImageButton` . En este ejemplo, se establece en un archivo local que se cargará desde cada proyecto de plataforma, lo que generará las siguientes capturas de pantallas:

[![ImageButton básico](imagebutton-images/BasicImageButton.png "ImageButton básico")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton básico")

De forma predeterminada, `ImageButton` es rectangular, pero puede asignarle esquinas redondeadas mediante la `CornerRadius` propiedad. Para obtener más información sobre la `ImageButton` apariencia, vea la [apariencia de ImageButton](#imagebutton-appearance).

> [!NOTE]
> Mientras `ImageButton` que un puede cargar un GIF animado, solo mostrará el primer fotograma del GIF.

En el ejemplo siguiente se muestra cómo crear una página que es funcionalmente equivalente al ejemplo XAML anterior, pero completamente en C#:

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

## <a name="handling-imagebutton-clicks"></a>Controlar los clics de ImageButton

`ImageButton`define un `Clicked` evento que se desencadena cuando el usuario puntea `ImageButton` con un dedo o un puntero del mouse. El evento se desencadena cuando el dedo o el botón del mouse se suelta de la superficie de `ImageButton` . El `ImageButton` debe tener su `IsEnabled` propiedad establecida en `true` para responder a los grifos.

En el ejemplo siguiente se muestra cómo crear una instancia `ImageButton` de en XAML y controlar su `Clicked` evento:

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

El `Clicked` evento se establece en un controlador de eventos denominado `OnImageButtonClicked` que se encuentra en el archivo de código subyacente:

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

Cuando se pulsa `ImageButton`, se ejecuta el método `OnImageButtonClicked`. El `sender` argumento es el `ImageButton` responsable de este evento. Puede utilizar esto para tener acceso al `ImageButton` objeto o para distinguir entre varios `ImageButton` objetos que comparten el mismo `Clicked` evento.

Este `Clicked` controlador determinado incrementa un contador y muestra el valor del contador en un [`Label`](xref:Xamarin.Forms.Label) :

[![Clic en ImageButton básico](imagebutton-images/ImageButton.png "Clic en ImageButton básico")](imagebutton-images/ImageButton-Large.png#lightbox "Clic en ImageButton básico")

En el ejemplo siguiente se muestra cómo crear una página que es funcionalmente equivalente al ejemplo XAML anterior, pero completamente en C#:

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

## <a name="disabling-the-imagebutton"></a>Deshabilitar ImageButton

A veces, una aplicación se encuentra en un estado determinado en el que un `ImageButton` clic determinado no es una operación válida. En esos casos, `ImageButton` se debe deshabilitar estableciendo su `IsEnabled` propiedad en `false` .

## <a name="using-the-command-interface"></a>Usar la interfaz de comandos

Es posible que una aplicación responda a `ImageButton` las pulsaciones sin controlar el `Clicked` evento. `ImageButton`Implementa un mecanismo de notificación alternativo denominado _comando_ o interfaz de _comandos_ . Consta de dos propiedades:

- `Command`de tipo [`ICommand`](xref:System.Windows.Input.ICommand) , una interfaz definida en el [`System.Windows.Input`](xref:System.Windows.Input) espacio de nombres.
- `CommandParameter`propiedad de tipo [`Object`](xref:System.Object) .

Este enfoque es adecuado en relación con el enlace de datos, especialmente cuando se implementa la arquitectura Model-View-ViewModel (MVVM).

Para obtener más información acerca del uso de la interfaz de comandos, consulte [uso de la interfaz de comandos](button.md#using-the-command-interface) en la guía de [botones](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Presionar y soltar el ImageButton

Además del evento `Clicked`, `ImageButton` también define los eventos `Pressed` y `Released`. El `Pressed` evento tiene lugar cuando se presiona un dedo en un control `ImageButton` , o cuando se presiona un botón del mouse con el puntero situado sobre `ImageButton` . El `Released` evento tiene lugar cuando se suelta el dedo o el botón del mouse. Normalmente, el `Clicked` evento también se desencadena al mismo tiempo que el `Released` evento, pero si el dedo o el puntero del mouse se deslizan fuera de la superficie de `ImageButton` antes de su lanzamiento, el `Clicked` evento podría no producirse.

Para obtener más información sobre estos eventos, vea [presionar y soltar el botón](button.md#pressing-and-releasing-the-button) en la guía de [botones](button.md) .

## <a name="imagebutton-appearance"></a>Apariencia de ImageButton

Además de las propiedades que `ImageButton` heredan de la [`View`](xref:Xamarin.Forms.View) clase, `ImageButton` también define varias propiedades que afectan a su apariencia:

- `Aspect`es el modo en que la imagen se escalará para ajustarse al área de presentación.
- `BorderColor`es el color de un área que rodea a `ImageButton` .
- `BorderWidth`es el ancho del borde.
- `CornerRadius`es el radio de la esquina del `ImageButton` .

La `Aspect` propiedad se puede establecer en uno de los miembros de la [`Aspect`](xref:Xamarin.Forms.Aspect) enumeración:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): estira la imagen para rellenar completamente el `ImageButton` . Esto puede dar lugar a que la imagen se distorsione.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-recorta la imagen para que rellene `ImageButton` mientras conserva la relación de aspecto.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-panorámica la imagen (si es necesario) para que toda la imagen quepa en el `ImageButton` , con un espacio en blanco agregado a la parte superior o inferior, dependiendo de si la imagen es ancha o alta. Este es el valor predeterminado de la [`Aspect`](xref:Xamarin.Forms.Aspect) enumeración.

> [!NOTE]
> La `ImageButton` clase también tiene [`Margin`](xref:Xamarin.Forms.View.Margin) `Padding` propiedades y que controlan el comportamiento de diseño de `ImageButton` . Para obtener más información, vea [Márgenes y relleno](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Estados visuales de ImageButton

`ImageButton`tiene un `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) que se puede utilizar para iniciar un cambio visual en `ImageButton` cuando lo presiona el usuario, siempre que esté habilitado.

En el siguiente ejemplo de XAML se muestra cómo definir un estado visual para el `Pressed` Estado:

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

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) Especifica que cuando `ImageButton` se presiona, su propiedad se [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) cambiará de su valor predeterminado de 1 a 0,8. `Normal` `VisualState` Especifica que cuando el `ImageButton` está en un estado normal, su `Scale` propiedad se establecerá en 1. Por lo tanto, el efecto general es que, cuando `ImageButton` se presiona, se vuelve a escalar para que sea ligeramente menor y, cuando `ImageButton` se libera, se vuelve a escalar a su tamaño predeterminado.

Para obtener más información sobre los Estados visuales, consulte [el Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
