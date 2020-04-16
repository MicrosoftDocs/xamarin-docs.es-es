---
title: Fuentes en Xamarin.Forms
description: En este artículo se explica cómo especificar información de fuente en controles que muestran texto en aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.openlocfilehash: ca4d3b242fcc73bb73e8d6ab1f817eefcc2ade4d
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/15/2020
ms.locfileid: "81388803"
---
# <a name="fonts-in-xamarinforms"></a>Fuentes en Xamarin.Forms

[![Descargar](~/media/shared/download.png) ejemplo Descargue el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

En este artículo se describe cómo Xamarin.Forms le permite especificar atributos de fuente (incluido el peso y el tamaño) en los controles que muestran texto. La información de fuente se puede [especificar en el código](#Setting_Font_in_Code) o en [XAML.](#Setting_Font_in_Xaml) También es posible utilizar una [fuente personalizada](#use-a-custom-font)y mostrar iconos de [fuente.](#display-font-icons)

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>Establezca la fuente en el código

Utilice las tres propiedades relacionadas con la fuente de los controles que muestran texto:

- **FontFamily** &ndash; `string` el nombre de fuente.
- **FontSize** &ndash; el tamaño `double`de fuente como un archivo .
- **FontAttributes** &ndash; una cadena que especifica información de `FontAttributes` estilo como *cursiva* y **negrita** (mediante la enumeración en C-).

Este código muestra cómo crear una etiqueta y especificar el tamaño y el peso de fuente que se mostrarán:

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Tamaño de fuente

La `FontSize` propiedad se puede establecer en un valor double, por ejemplo:

```csharp
label.FontSize = 24;
```

El valor de tamaño se mide en unidades independientes del dispositivo. Para obtener más información, consulte [Unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Forms también define [`NamedSize`](xref:Xamarin.Forms.NamedSize) campos en la enumeración que representan tamaños de fuente específicos. Para obtener más información acerca de los tamaños de fuente con nombre, consulte [Tamaños](#named-font-sizes)de fuente con nombre .

<a name="FontAttributes" />

### <a name="font-attributes"></a>Atributos de fuente

Los estilos de fuente, como `FontAttributes` **negrita** y *cursiva,* se pueden establecer en la propiedad. Actualmente se admiten los siguientes valores:

- **None**
- **Negrita**
- **Cursiva**

La `FontAttribute` enumeración se puede utilizar de la siguiente `OR` manera (puede especificar un único atributo o juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Establecer información de fuente por plataforma

Como alternativa, `Device.RuntimePlatform` la propiedad se puede utilizar para establecer diferentes nombres de fuente en cada plataforma, como se muestra en este código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Una buena fuente información para iOS es [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>Establecer la fuente en XAML

Xamarin.Forms controles que muestran `FontSize` texto todos tienen una propiedad que se puede establecer en XAML. La forma más sencilla de establecer la fuente en XAML es usar los valores de enumeración de tamaño con nombre, como se muestra en este ejemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Hay un convertidor integrado para `FontSize` la propiedad que permite que toda la configuración de fuente se exprese como un valor de cadena en XAML. Además, `FontAttributes` la propiedad se puede utilizar para especificar atributos de fuente:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) propiedad también se puede usar en XAML para representar una fuente diferente en cada plataforma. El ejemplo siguiente utiliza diferentes fuentes en cada plataforma:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="named-font-sizes"></a>Tamaños de fuente con nombre

Xamarin.Forms define campos [`NamedSize`](xref:Xamarin.Forms.NamedSize) en la enumeración que representan tamaños de fuente específicos. En la tabla `NamedSize` siguiente se muestran los miembros y sus tamaños predeterminados en iOS, Android y la Plataforma universal de Windows (UWP):

| Member | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Los valores de tamaño se miden en unidades independientes del dispositivo. Para obtener más información, consulte [Unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Los tamaños de fuente con nombre se pueden establecer a través de XAML y código. Además, `Device.GetNamedSize` se puede llamar `double` al método para devolver un que representa el tamaño de fuente con nombre:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> En iOS y Android, los tamaños de fuente con nombre se escalarán automáticamente en función de las opciones de accesibilidad del sistema operativo. Este comportamiento se puede deshabilitar en iOS con una plataforma específica. Para obtener más información, consulte Escalado de accesibilidad para tamaños de [fuente con nombre en iOS.](~/xamarin-forms/platform/ios/named-font-size-scaling.md)

## <a name="use-a-custom-font"></a>Usar una fuente personalizada

Las fuentes personalizadas se pueden agregar al proyecto compartido de Xamarin.Forms y se consumen por proyectos de plataforma sin ningún trabajo adicional. El proceso para llevarlo a cabo es el siguiente:

1. Agregue la fuente al proyecto compartido de Xamarin.Forms como un recurso incrustado ( Acción de**compilación: EmbeddedResource**).
1. Registre el archivo de fuente con el **AssemblyInfo.cs**ensamblado, `ExportFont` en un archivo como AssemblyInfo.cs , utilizando el atributo. También se puede especificar un alias opcional.

> [!IMPORTANT]
> Las fuentes incrustadas requieren el uso de Xamarin.Forms 4.5.0.530 o superior.

En el ejemplo siguiente se muestra la fuente Lobster-Regular que se registra con el ensamblado, junto con un alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> La fuente puede residir en cualquier carpeta del proyecto compartido, sin tener que especificar el nombre de la carpeta al registrar la fuente con el ensamblado.

La fuente se puede consumir en cada plataforma haciendo referencia a su nombre, sin la extensión de archivo:

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Alternativamente, se puede consumir en cada plataforma haciendo referencia a su alias:

```xaml
<!-- Use font alias -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster" />
```

El código de C# equivalente es el siguiente:

```csharp
// Use font name
Label label1 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};

// Use font alias
Label label2 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster"
};
```

Las siguientes capturas de pantalla muestran la fuente personalizada:

[![Fuente personalizada en iOS y Android](fonts-images/custom-sml.png "Ejemplo de fuentes personalizadas")](fonts-images/custom.png#lightbox "Ejemplo de fuentes personalizadas")

> [!IMPORTANT]
> En Windows, el nombre del archivo de fuente y el nombre de fuente pueden ser diferentes. Para descubrir el nombre de fuente en Windows, haga clic con el botón derecho en el archivo .ttf y seleccione **Vista previa**. El nombre de la fuente se puede determinar desde la ventana de vista previa.

## <a name="display-font-icons"></a>Presentación iconos de fuente

Los iconos de fuente se pueden mostrar mediante las aplicaciones de `FontImageSource` Xamarin.Forms especificando los datos de icono de fuente en un objeto. Esta clase, que deriva [`ImageSource`](xref:Xamarin.Forms.ImageSource) de la clase, tiene las siguientes propiedades:

- `Glyph`– el valor de carácter unicode del `string`icono de fuente, especificado como .
- `Size`– `double` un valor que indica el tamaño, en unidades independientes del dispositivo, del icono de fuente renderizada. El valor predeterminado es 30. Además, esta propiedad se puede establecer en un tamaño de fuente con nombre.
- `FontFamily`– `string` una representación de la familia de fuentes a la que pertenece el icono de fuente.
- `Color`– un [`Color`](xref:Xamarin.Forms.Color) valor opcional que se utilizará al mostrar el icono de fuente.

Estos datos se utilizan para crear un PNG, que puede `ImageSource`ser mostrado por cualquier vista que pueda mostrar un archivo . Este enfoque permite que los iconos de fuente, como los emojis, se muestren en varias vistas, en lugar [`Label`](xref:Xamarin.Forms.Label)de limitar la visualización del icono de fuente a una vista de presentación de texto único, como un archivo .

> [!IMPORTANT]
> Los iconos de fuente solo se pueden especificar actualmente mediante su representación de caracteres unicode.

El siguiente ejemplo XAML tiene un único icono [`Image`](xref:Xamarin.Forms.Image) de fuente que muestra una vista:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Este código muestra un icono XBox, de la familia [`Image`](xref:Xamarin.Forms.Image) de fuentes Ionicons, en una vista. Tenga en cuenta que mientras `\uf30c`que el carácter unicode para este `&#xf30c;`icono es , tiene que ser de escape en XAML y así se convierte en . El código de C# equivalente es el siguiente:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

Las siguientes capturas de pantalla, del ejemplo [Diseños enlazables,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) muestran varios iconos de fuente que se muestran mediante un diseño enlazable:

![Captura de pantalla de los iconos de fuente que se muestran, en iOS y Android](fonts-images/font-image-source.png "Iconos de fuente que se muestran en una vista de imagen")

## <a name="related-links"></a>Vínculos relacionados

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (muestra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Diseños enlazables (muestra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Diseños enlazables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
