---
title: Fuentes en Xamarin.Forms
description: En este artículo se explica cómo especificar la información de fuentes en los controles que muestran texto en Xamarin.Forms aplicaciones.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2020
ms.custom: contperf-fy21q2
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 16da3964a565428f5e80924684ed073886c5386c
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2021
ms.locfileid: "99540979"
---
# <a name="fonts-in-xamarinforms"></a>Fuentes en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/workingwithfonts)

De forma predeterminada, Xamarin.Forms usa una fuente del sistema definida por cada plataforma. Sin embargo, los controles que muestran texto definen propiedades que puede usar para cambiar esta fuente:

- `FontAttributes`, de tipo `FontAttributes` , que es una enumeración con tres miembros: `None` , `Bold` y `Italic` . El valor predeterminado de esta propiedad es `None`.
- `FontSize`, de tipo `double`.
- `FontFamily`, de tipo `string`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

## <a name="set-font-attributes"></a>Establecer atributos de fuente

Los controles que muestran texto pueden establecer la `FontAttributes` propiedad para especificar los atributos de fuente:

```xaml
<Label Text="Italics"
       FontAttributes="Italic" />
<Label Text="Bold and italics"
       FontAttributes="Bold, Italic" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label1 = new Label
{
    Text = "Italics",
    FontAttributes = FontAttributes.Italic
};

Label label2 = new Label
{
    Text = "Bold and italics",
    FontAttributes = FontAttributes.Bold | FontAttributes.Italic
};    
```

## <a name="set-the-font-size"></a>Establecer el tamaño de fuente

Los controles que muestran texto pueden establecer la `FontSize` propiedad para especificar el tamaño de fuente. La `FontSize` propiedad se puede establecer en un `double` valor directamente o en un [`NamedSize`](xref:Xamarin.Forms.NamedSize) valor de enumeración:

```xaml
<Label Text="Font size 24"
       FontSize="24" />
<Label Text="Large font size"
       FontSize="Large" />
```

El código de C# equivalente es el siguiente:

```csharp
Label label1 = new Label
{
    Text = "Font size 24",
    FontSize = 24
};

Label label2 = new Label
{
    Text = "Large font size",
    FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label))
};
```

Como alternativa, el `Device.GetNamedSize` método tiene una invalidación que especifica el segundo argumento como [`Element`](xref:Xamarin.Forms.Element) :

```csharp
Label myLabel = new Label
{
    Text = "Large font size",
};
myLabel.FontSize = Device.GetNamedSize(NamedSize.Large, myLabel);
```

> [!NOTE]
> El `FontSize` valor, cuando se especifica como `double` , se mide en unidades independientes del dispositivo. Para obtener más información, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Para obtener más información sobre los tamaños de fuente con nombre, vea [comprender los tamaños de fuente con nombre](#understand-named-font-sizes).

## <a name="set-the-font-family"></a>Establecimiento de la familia de fuentes

Los controles que muestran texto pueden establecer la `FontFamily` propiedad en un nombre de familia de fuentes, como "Times Roman". Sin embargo, esto solo funcionará si se admite esa familia de fuentes en la plataforma en cuestión.

Hay una serie de técnicas que se pueden usar para intentar derivar las fuentes que están disponibles en una plataforma. Sin embargo, la presencia de un archivo de fuente TTF (True Type Format) no implica necesariamente una familia de fuentes, y a menudo se incluyen TTFs que no están diseñados para su uso en aplicaciones. Además, las fuentes instaladas en una plataforma pueden cambiar con la versión de la plataforma. Por lo tanto, el enfoque más confiable para especificar una familia de fuentes es usar una fuente personalizada.

Las fuentes personalizadas se pueden agregar al Xamarin.Forms proyecto compartido y usarse en proyectos de plataforma sin ningún trabajo adicional. El proceso para llevarlo a cabo es el siguiente:

1. Agregue la fuente al Xamarin.Forms proyecto compartido como un recurso incrustado (**acción de compilación: EmbeddedResource**).
1. Registre el archivo de fuente con el ensamblado, en un archivo como **AssemblyInfo.CS**, mediante el `ExportFont` atributo. También se puede especificar un alias opcional.

En el ejemplo siguiente se muestra la fuente Lobster-Regular que se registra con el ensamblado, junto con un alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> La fuente puede residir en cualquier carpeta del proyecto compartido, sin tener que especificar el nombre de la carpeta al registrar la fuente con el ensamblado.
>
> En Windows, el nombre del archivo de fuente y el nombre de fuente pueden ser diferentes. Para detectar el nombre de fuente en Windows, haga clic con el botón secundario en el archivo. ttf y seleccione **vista previa**. El nombre de la fuente se puede determinar a continuación en la ventana de vista previa.

La fuente se puede consumir después en cada plataforma haciendo referencia a su nombre, sin la extensión de archivo:

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

Como alternativa, se puede consumir en cada plataforma haciendo referencia a su alias:

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

Las siguientes capturas de pantallas muestran la fuente personalizada:

[![Fuente personalizada en iOS y Android](fonts-images/custom-sml.png "Ejemplo de fuentes personalizadas")](fonts-images/custom.png#lightbox "Ejemplo de fuentes personalizadas")

> [!IMPORTANT]
> En el caso de las compilaciones de versión en Windows, asegúrese de que el ensamblado que contiene la fuente personalizada se pasa como argumento en la `Forms.Init` llamada al método. Para más información, consulte [Solución de problemas](~/xamarin-forms/platform/windows/installation/index.md#troubleshooting).

## <a name="set-font-properties-per-platform"></a>Establecer propiedades de fuente por plataforma

Las [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) clases y se pueden usar en XAML para establecer las propiedades de fuente por plataforma. En el ejemplo siguiente se establecen diferentes familias y tamaños de fuente en cada plataforma:

```xaml
<Label Text="Different font properties on different platforms"
       FontSize="{OnPlatform iOS=20, Android=Medium, UWP=24}">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
            <On Platform="iOS" Value="MarkerFelt-Thin" />
            <On Platform="Android" Value="Lobster-Regular" />
            <On Platform="UWP" Value="ArimaMadurai-Black" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) propiedad se puede utilizar en el código para establecer las propiedades de fuente por plataforma.

```csharp
Label label = new Label
{
    Text = "Different font properties on different platforms"
};

label.FontSize = Device.RuntimePlatform == Device.iOS ? 20 :
    Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : 24;
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular" : "ArimaMadurai-Black";
```

Para obtener más información sobre cómo proporcionar valores específicos de la plataforma, vea [proporcionar valores específicos de la plataforma](~/xamarin-forms/platform/device.md#provide-platform-specific-values). Para obtener información sobre la `OnPlatform` extensión de marcado, consulte [extensión de marcado](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)en la plataforma.

## <a name="understand-named-font-sizes"></a>Comprender los tamaños de fuente con nombre

Xamarin.Forms define los campos de la [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumeración que representan tamaños de fuente concretos. En la tabla siguiente se muestran los `NamedSize` miembros y sus tamaños predeterminados en iOS, Android y el plataforma universal de Windows (UWP):

| Miembro | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 17 | 14 | 14 |
| `Micro` | 12 | 10 | 15,667 |
| `Small` | 14 | 14 | 18,667 |
| `Medium` | 17 | 17 | 22,667 |
| `Large` | 22 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Los valores de tamaño se miden en unidades independientes del dispositivo. Para obtener más información, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

> [!NOTE]
> En iOS y Android, los tamaños de fuente con nombre se escalan automáticamente en función de las opciones de accesibilidad del sistema operativo. Este comportamiento se puede deshabilitar en iOS con una plataforma específica. Para obtener más información, vea [escalado de accesibilidad para tamaños de fuente con nombre en iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="display-font-icons"></a>Presentación iconos de fuente

Las aplicaciones pueden mostrar iconos Xamarin.Forms de fuente especificando los datos del icono de fuente en un `FontImageSource` objeto. Esta clase, que se deriva de la [`ImageSource`](xref:Xamarin.Forms.ImageSource) clase, tiene las propiedades siguientes:

- `Glyph` : el valor del carácter Unicode del icono de fuente, especificado como `string` .
- `Size` : un `double` valor que indica el tamaño, en unidades independientes del dispositivo, del icono de fuente representada. El valor predeterminado es 30. Además, esta propiedad se puede establecer en un tamaño de fuente con nombre.
- `FontFamily` : `string` que representa la familia de fuentes a la que pertenece el icono de fuente.
- `Color` : un [`Color`](xref:Xamarin.Forms.Color) valor opcional que se usará al mostrar el icono de fuente.

Estos datos se usan para crear un PNG, que puede mostrarse en cualquier vista que muestre un `ImageSource` . Este enfoque permite que varias vistas muestren iconos de fuente, como emojis, en lugar de limitar la presentación del icono de fuente a una única vista de presentación de texto, como [`Label`](xref:Xamarin.Forms.Label) .

> [!IMPORTANT]
> Los iconos de fuente solo se pueden especificar actualmente mediante su representación de caracteres Unicode.

En el siguiente ejemplo de XAML se muestra un icono de fuente único en una [`Image`](xref:Xamarin.Forms.Image) vista:

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

Este código muestra un icono XBox, de la familia de fuentes Ionicons, en una [`Image`](xref:Xamarin.Forms.Image) vista. Tenga en cuenta que, mientras que el carácter Unicode para este icono es `\uf30c` , tiene que incluir un carácter de escape en XAML, por lo que se convierte en `&#xf30c;` . El código de C# equivalente es el siguiente:

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

En las siguientes capturas de pantalla, desde el ejemplo de [diseños enlazables](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) , se muestran varios iconos de fuente mostrados por un diseño enlazable:

![Captura de pantalla de los iconos de fuente que se muestran, en iOS y Android](fonts-images/font-image-source.png "Iconos de fuente que se muestran en una vista de imagen")

## <a name="related-links"></a>Vínculos relacionados

- [FontsSample](/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Diseños enlazables (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Proporcionar valores específicos de la plataforma](~/xamarin-forms/platform/device.md#provide-platform-specific-values)
- [Extensión de marcado en la plataforma](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)
- [Diseños enlazables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
