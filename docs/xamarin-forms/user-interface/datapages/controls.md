---
title: Referencia de controles de páginas de página
description: En este artículo se presentan los controles que están disponibles en el Xamarin.Forms paquete NuGet de páginas de página.
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e206c89de278c8b85848294e76e4431825eaee85
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938962"
---
# <a name="datapages-controls-reference"></a>Referencia de controles de páginas de página

![Esta API está actualmente en versión preliminar](~/media/shared/preview.png)

> [!IMPORTANT]
> Las páginas de página requieren una Xamarin.Forms referencia de tema que se va a representar. Esto implica la instalación de [ Xamarin.Forms . Paquete NuGet Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) en el proyecto, seguido de [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [ Xamarin.Forms . Paquetes NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Xamarin.FormsNuGet de páginas de datos incluye una serie de controles que pueden aprovechar el enlace de origen de datos.

Para usar estos controles en XAML, asegúrese de que se ha incluido el espacio de nombres, por ejemplo, vea la `xmlns:pages` declaración siguiente:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

En los ejemplos siguientes `DynamicResource` se incluyen referencias que deben existir en el Diccionario de recursos del proyecto para que funcione. También hay un ejemplo de cómo crear un [control personalizado](#custom-control-example).

## <a name="built-in-controls"></a>Controles integrados

* [HeroImage](#heroimage)
* [ListItem](#listitem)

### <a name="heroimage"></a>HeroImage

El `HeroImage` control tiene cuatro propiedades:

* Texto
* Detalle
* ImageSource
* Aspecto

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![Control HeroImage en Android](controls-images/heroimage-light-android.png) ![Control HeroImage en Android](controls-images/heroimage-dark-android.png)

**iOS**

![Control HeroImage en iOS](controls-images/heroimage-light-ios.png) ![Control HeroImage en iOS](controls-images/heroimage-dark-ios.png)

### <a name="listitem"></a>ListItem

El `ListItem` diseño del control es similar a las filas nativas de la tabla o la lista de iOS y Android, pero también se puede usar como vista normal. En el código de ejemplo siguiente, se muestra hospedado en `StackLayout` , pero también se puede usar en controles de lista de scolling enlazados a datos.

Hay cinco propiedades:

* Title
* Detalle
* ImageSource
* PlaceholdImageSource
* Aspecto

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

Estas capturas de pantallas muestran `ListItem` en las plataformas iOS y Android mediante los temas claro y oscuro:

**Android**

![Control ListItem en Android](controls-images/listitem-light-android.png) ![Control ListItem en Android](controls-images/listitem-dark-android.png)

**iOS**

![Control ListItem en iOS](controls-images/listitem-light-ios.png) ![Control ListItem en iOS](controls-images/listitem-dark-ios.png)

## <a name="custom-control-example"></a>Ejemplo de control personalizado

El objetivo de este `CardView` control personalizado es similar al CardView nativo de Android.

Contendrá tres propiedades:

* Texto
* Detalle
* ImageSource

El objetivo es un control personalizado que se parecerá al código siguiente (tenga en cuenta que `xmlns:local` se requiere un personalizado que haga referencia al ensamblado actual):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Debe ser similar a las capturas de pantallas siguientes con los colores correspondientes a los temas de luz y oscuridad integrados:

**Android**

![Control personalizado CardView en Android](controls-images/cardview-light-android.png) ![Control personalizado CardView en Android](controls-images/cardview-dark-android.png)

**iOS**

![Control personalizado CardView en iOS](controls-images/cardview-light-ios.png) ![Control personalizado CardView en iOS](controls-images/cardview-dark-ios.png)

### <a name="building-the-custom-cardview"></a>Compilar el CardView personalizado

1. [DataView (subclase)](#1-dataview-subclass)
2. [Definir fuente, diseño y márgenes](#2-define-font-layout-and-margins)
3. [Crear estilos para los elementos secundarios del control](#3-create-styles-for-the-controls-children)
4. [Crear la plantilla de diseño de control](#4-create-the-control-layout-template)
5. [Agregar los recursos específicos del tema](#5-add-the-theme-specific-resources)
6. [Establecer ControlTemplate para la clase CardView](#6-set-the-controltemplate-for-the-cardview-class)
7. [Agregar el control a una página](#7-add-the-control-to-a-page)

#### <a name="1-dataview-subclass"></a>1. subclase DataView

La subclase de C# de `DataView` define las propiedades enlazables del control.

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

#### <a name="2-define-font-layout-and-margins"></a>2. definir la fuente, el diseño y los márgenes

El diseñador de controles descifraría estos valores como parte del diseño de la interfaz de usuario para el control personalizado. Cuando se requieren especificaciones específicas de la plataforma, `OnPlatform` se usa el elemento.

Tenga en cuenta que algunos valores hacen referencia a `StaticResource` s: se definirán en el [paso 5](#5-add-the-theme-specific-resources).

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

#### <a name="3-create-styles-for-the-controls-children"></a>3. crear estilos para los elementos secundarios del control

Haga referencia a todos los elementos definidos sobre para crear los elementos secundarios que se usarán en el control personalizado:

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

#### <a name="4-create-the-control-layout-template"></a>4. crear la plantilla de diseño de control

El diseño visual del control personalizado se declara explícitamente en la plantilla de control mediante los recursos definidos anteriormente:

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />

    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

#### <a name="5-add-the-theme-specific-resources"></a>5. agregar los recursos específicos del tema

Dado que se trata de un control personalizado, agregue los recursos que coinciden con el tema que usa el Diccionario de recursos:

##### <a name="light-theme-colors"></a>Colores de tema claro

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>Colores del tema oscuro

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. establecer ControlTemplate para la clase CardView

Por último, asegúrese de que la clase de C# creada en el [paso 1](#1-dataview-subclass) usa la plantilla de control definida en el [paso 4](#4-create-the-control-layout-template) mediante un `Style` `Setter` elemento.

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

#### <a name="7-add-the-control-to-a-page"></a>7. agregar el control a una página

`CardView`Ahora se puede Agregar el control a una página. En el ejemplo siguiente se muestra que se hospeda en un `StackLayout` :

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
