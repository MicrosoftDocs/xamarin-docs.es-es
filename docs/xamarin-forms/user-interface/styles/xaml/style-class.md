---
title: " Xamarin.Forms clases de estilo" Description: " Xamarin.Forms las clases de estilo permiten aplicar varios estilos a un control, sin tener que recurrir a la herencia de estilo".
MS. Prod: Xamarin ms. AssetID: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/30/2019 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-style-classes"></a>Clases de estilo de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Las clases de estilo de Xamarin. Forms permiten aplicar varios estilos a un control, sin tener que recurrir a la herencia de estilo._

## <a name="create-style-classes"></a>Crear clases de estilo

Se puede crear una clase de estilo estableciendo la [`Class`](xref:Xamarin.Forms.Style.Class) propiedad de en [`Style`](xref:Xamarin.Forms.Style) un `string` que representa el nombre de clase. La ventaja que ofrece esto, a través de la definición de un estilo explícito mediante el `x:Key` atributo, es que se pueden aplicar varias clases de estilo a [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!IMPORTANT]
> Varios estilos pueden compartir el mismo nombre de clase, siempre que tengan como destino tipos diferentes. Esto permite que varias clases de estilo, que tienen un nombre idéntico, tengan como destino tipos diferentes.

En el ejemplo siguiente se muestran tres [`BoxView`](xref:Xamarin.Forms.BoxView) clases de estilo y una [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase de estilo:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

Las `Separator` `Rounded` clases de estilo, y `Circle` establecen [`BoxView`](xref:Xamarin.Forms.BoxView) las propiedades en valores específicos.

La `Rotated` clase de estilo tiene un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , lo que significa que solo se puede aplicar a `VisualElement` las instancias de. Sin embargo, su [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propiedad se establece en `true` , lo que garantiza que se puede aplicar a los controles que derivan de `VisualElement` , como [`BoxView`](xref:Xamarin.Forms.BoxView) . Para obtener más información sobre cómo aplicar un estilo a un tipo derivado, vea [aplicar un estilo a los tipos derivados](implicit.md#apply-a-style-to-derived-types).

El código de C# equivalente es el siguiente:

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>Usar clases de estilo

Las clases de estilo se pueden utilizar estableciendo la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propiedad del control, que es de tipo `IList<string>` , en una lista de nombres de clase de estilo. Se aplicarán las clases de estilo, siempre que el tipo del control coincida con el [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de las clases de estilo.

En el ejemplo siguiente se muestran tres [`BoxView`](xref:Xamarin.Forms.BoxView) instancias, cada una de ellas establecida en clases de estilo diferentes:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

En este ejemplo, el primero [`BoxView`](xref:Xamarin.Forms.BoxView) tiene el estilo de un separador de línea, mientras que el tercero `BoxView` es circular. La segunda `BoxView` tiene dos clases de estilo aplicadas, que proporcionan esquinas redondeadas y giran 45 grados:

![BoxViews con estilo de clases de estilo](style-class-images/boxviews.png)

> [!IMPORTANT]
> Se pueden aplicar varias clases de estilo a un control porque la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propiedad es de tipo `IList<string>` . Cuando esto ocurre, las clases de estilo se aplican en orden de lista ascendente. Por lo tanto, cuando varias clases de estilo establecen propiedades idénticas, la propiedad de la clase de estilo que se encuentra en la posición de la lista más alta tendrá prioridad.

El código de C# equivalente es el siguiente:

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>Vínculos relacionados

- [Estilos básicos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
