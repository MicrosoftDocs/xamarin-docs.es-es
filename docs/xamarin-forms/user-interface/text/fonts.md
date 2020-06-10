---
title: "Fonts in Xamarin.Forms " Description: "en este artículo se explica cómo especificar la información de fuentes en los controles que muestran texto en Xamarin.Forms aplicaciones."
MS. Prod: Xamarin ms. AssetID: 49DD2249-C575-41AE-AE06-08F890FD6031 ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/01/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="fonts-in-xamarinforms"></a>Fuentes enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

En este artículo se describe cómo Xamarin.Forms permite especificar los atributos de fuente (incluido el peso y el tamaño) en los controles que muestran texto. La información de fuente se puede [especificar en el código](#set-the-font-in-code) o [especificarse en XAML](#set-the-font-in-xaml). También es posible usar una [fuente personalizada](#use-a-custom-font)y [Mostrar iconos de fuente](#display-font-icons).

## <a name="set-the-font-in-code"></a>Establecer la fuente en el código

Use las tres propiedades relacionadas con la fuente de los controles que muestran texto:

- **FontFamily** &ndash; nombre de la `string` fuente.
- **FontSize** &ndash; el tamaño de fuente como `double` .
- **FontAttributes** &ndash; cadena que especifica la información de estilo como *cursiva* y **negrita** (usando la `FontAttributes` enumeración en C#).

Este código muestra cómo crear una etiqueta y especificar el tamaño y el peso de la fuente que se va a mostrar:

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

### <a name="font-size"></a>Tamaño de fuente

La `FontSize` propiedad se puede establecer en un valor doble, por ejemplo:

```csharp
label.FontSize = 24;
```

El valor de tamaño se mide en unidades independientes del dispositivo. Para obtener más información, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Xamarin.Formstambién define los campos de la [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumeración que representan tamaños de fuente específicos. Para obtener más información sobre los tamaños de fuente con nombre, vea [tamaños de fuente con nombre](#named-font-sizes).

### <a name="font-attributes"></a>Atributos de fuente

Los estilos de fuente, como **Bold** y *Italic* , se pueden establecer en la `FontAttributes` propiedad. Actualmente se admiten los siguientes valores:

- **Ninguno**
- **Bold**
- **Aplicar**

La `FontAttribute` enumeración se puede usar como se indica a continuación (puede especificar un único atributo o `OR` ambos juntos):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>Establecer la información de fuente por plataforma

Como alternativa, `Device.RuntimePlatform` se puede usar la propiedad para establecer diferentes nombres de fuente en cada plataforma, como se muestra en este código:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Una buena fuente de información de fuentes para iOS es [iosfonts.com](http://iosfonts.com).

## <a name="set-the-font-in-xaml"></a>Establecer la fuente en XAML

Xamarin.Formstodos los controles que muestran texto tienen una `FontSize` propiedad que se puede establecer en XAML. La manera más sencilla de establecer la fuente en XAML es usar los valores de enumeración de tamaño con nombre, como se muestra en este ejemplo:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Existe un convertidor integrado para la `FontSize` propiedad que permite expresar toda la configuración de fuente como un valor de cadena en XAML. Además, la `FontAttributes` propiedad se puede utilizar para especificar los atributos de fuente:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

La [`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values) propiedad también se puede usar en XAML para representar una fuente diferente en cada plataforma. En el ejemplo siguiente se usan fuentes diferentes en cada plataforma:

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

Xamarin.Formsdefine los campos de la [`NamedSize`](xref:Xamarin.Forms.NamedSize) enumeración que representan tamaños de fuente concretos. En la tabla siguiente se muestran los `NamedSize` miembros y sus tamaños predeterminados en iOS, Android y el plataforma universal de Windows (UWP):

| Miembro | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15,667 |
| `Small` | 13 | 14 | 18,667 |
| `Medium` | 16 | 17 | 22,667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

Los valores de tamaño se miden en unidades independientes del dispositivo. Para obtener más información, consulte [unidades de medida](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement).

Los tamaños de fuente con nombre se pueden establecer a través de XAML y código. Además, `Device.GetNamedSize` se puede llamar al método para devolver un `double` que representa el tamaño de fuente con nombre:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> En iOS y Android, los tamaños de fuente con nombre se escalan automáticamente en función de las opciones de accesibilidad del sistema operativo. Este comportamiento se puede deshabilitar en iOS con una plataforma específica. Para obtener más información, vea [escalado de accesibilidad para tamaños de fuente con nombre en iOS](~/xamarin-forms/platform/ios/named-font-size-scaling.md).

## <a name="use-a-custom-font"></a>Usar una fuente personalizada

Las fuentes personalizadas se pueden agregar al Xamarin.Forms proyecto compartido y usarse en proyectos de plataforma sin ningún trabajo adicional. El proceso para llevarlo a cabo es el siguiente:

1. Agregue la fuente al Xamarin.Forms proyecto compartido como un recurso incrustado (**acción de compilación: EmbeddedResource**).
1. Registre el archivo de fuente con el ensamblado, en un archivo como **AssemblyInfo.CS**, mediante el `ExportFont` atributo. También se puede especificar un alias opcional.

> [!IMPORTANT]
> Las fuentes incrustadas requieren el uso de Xamarin.Forms 4.5.0.530 o superior.

En el ejemplo siguiente se muestra la fuente Lobster-normal que se registra con el ensamblado, junto con un alias:

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> La fuente puede residir en cualquier carpeta del proyecto compartido, sin tener que especificar el nombre de la carpeta al registrar la fuente con el ensamblado.

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
> En Windows, el nombre del archivo de fuente y el nombre de fuente pueden ser diferentes. Para detectar el nombre de fuente en Windows, haga clic con el botón secundario en el archivo. ttf y seleccione **vista previa**. El nombre de la fuente se puede determinar a continuación en la ventana de vista previa.

## <a name="display-font-icons"></a>Presentación iconos de fuente

Las aplicaciones pueden mostrar iconos Xamarin.Forms de fuente especificando los datos del icono de fuente en un `FontImageSource` objeto. Esta clase, que se deriva de la [`ImageSource`](xref:Xamarin.Forms.ImageSource) clase, tiene las propiedades siguientes:

- `Glyph`: el valor del carácter Unicode del icono de fuente, especificado como `string` .
- `Size`: un `double` valor que indica el tamaño, en unidades independientes del dispositivo, del icono de fuente representada. El valor predeterminado es 30. Además, esta propiedad se puede establecer en un tamaño de fuente con nombre.
- `FontFamily`: `string` que representa la familia de fuentes a la que pertenece el icono de fuente.
- `Color`: un [`Color`](xref:Xamarin.Forms.Color) valor opcional que se usará al mostrar el icono de fuente.

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

En las siguientes capturas de pantalla, desde el ejemplo de [diseños enlazables](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) , se muestran varios iconos de fuente mostrados por un diseño enlazable:

![Captura de pantalla de los iconos de fuente que se muestran, en iOS y Android](fonts-images/font-image-source.png "Iconos de fuente que se muestran en una vista de imagen")

## <a name="related-links"></a>Vínculos relacionados

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Texto (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Diseños enlazables (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Diseños enlazables](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
