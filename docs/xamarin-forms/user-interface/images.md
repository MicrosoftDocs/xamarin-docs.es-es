---
title: Imágenes en Xamarin.Forms
description: Las imágenes se pueden compartir entre plataformas con Xamarin.Forms, se pueden cargar específicamente para cada plataforma, o se pueden descargar para su presentación.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 255d3f2f532e4899b1a890405af942a7ca2da8ea
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305712"
---
# <a name="images-in-xamarinforms"></a>Imágenes en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Las imágenes se pueden compartir entre plataformas con Xamarin. Forms, se pueden cargar específicamente para cada plataforma o se pueden descargar para su visualización._

Las imágenes son una parte fundamental de navegación de la aplicación, la facilidad de uso y la personalización de marca. Las aplicaciones de Xamarin.Forms deben ser capaz de compartir imágenes en todas las plataformas, pero también puede mostrar diferentes imágenes en cada plataforma.

También se requieren para iconos y pantallas de presentación; las imágenes específicas de plataforma estas deben configurarse según el acuerdo con la plataforma.

## <a name="display-images"></a>Mostrar imágenes

Xamarin. Forms usa la vista de [`Image`](xref:Xamarin.Forms.Image) para mostrar imágenes en una página. Tiene dos propiedades importantes:

- [`Source`](xref:Xamarin.Forms.Image.Source) -una instancia de [`ImageSource`](xref:Xamarin.Forms.ImageSource) , archivo, Uri o recurso, que establece la imagen que se va a mostrar.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) : Cómo ajustar el tamaño de la imagen dentro de los límites en los que se muestra (si se va a expandir, recortar o Mostrar en pantalla ancha).

[`ImageSource`](xref:Xamarin.Forms.ImageSource) instancias se pueden obtener mediante métodos estáticos para cada tipo de origen de imagen:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) : requiere un nombre de archivo o una ruta de archivo que se pueda resolver en cada plataforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) : requiere un objeto URI, p. ej.,  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) : requiere un identificador de recursos para un archivo de imagen incrustado en el proyecto de biblioteca de aplicación o .net Standard, con una **acción de compilación: EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) : requiere una secuencia que proporcione datos de imagen.

La propiedad [`Aspect`](xref:Xamarin.Forms.Image.Aspect) determina cómo se escalará la imagen para ajustarse al área de presentación:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) : ajusta la imagen para que rellene completamente y exactamente el área de presentación. Esto puede dar lugar a que la imagen se distorsiona.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) : recorta la imagen para que rellene el área de presentación mientras conserva el aspecto (es decir, sin distorsión).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) : muestra la imagen (si es necesario) para que toda la imagen quepa en el área de visualización, con un espacio en blanco agregado a la parte superior o inferior, en función de si la imagen es ancha o alta.

Las imágenes se pueden cargar desde un [archivo local](#local-images), un [recurso incrustado](#embedded-images), [descargado](#download-images)o cargado desde una secuencia. Además, los iconos de fuente se pueden mostrar en la vista [`Image`](xref:Xamarin.Forms.Image) especificando los datos del icono de fuente en un objeto `FontImageSource`. Para obtener más información, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) en la guía de [fuentes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Imágenes locales

Archivos de imagen se pueden agregar a cada proyecto de aplicación y hacer referencia desde el código de Xamarin.Forms compartido. Este método de distribución de imágenes es necesario cuando las imágenes son específicas de la plataforma, por ejemplo, al usar resoluciones diferentes en plataformas diversas, o bien al emplear diseños que difieren ligeramente.

Para usar una sola imagen en todas las aplicaciones, se *debe usar el mismo nombre de archivo en*todas las plataformas y debe ser un nombre de recurso de Android válido (es decir, solo letras minúsculas, números, el carácter de subrayado y el período permitido).

- **iOS** : la manera preferida de administrar y admitir imágenes ya que Ios 9 es usar **conjuntos de imágenes del catálogo de activos**, que deben contener todas las versiones de una imagen necesarias para admitir varios dispositivos y factores de escala para una aplicación. Para obtener más información, consulte [Agregar imágenes a un conjunto de imágenes del catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** : incluya imágenes en el directorio **Resources/drawable** con la **acción de compilación: AndroidResource**. También se pueden proporcionar versiones alta y baja de PPP de una imagen (en **los** subdirectorios de recursos con nombre adecuados, como **drawable-ldpi**, **drawable-hdpi**y **drawable-xhdpi**).
- **Plataforma universal de Windows (UWP)** : Coloque imágenes en el directorio raíz de la aplicación con la **acción de compilación: contenido**.

> [!IMPORTANT]
> Antes de iOS 9, las imágenes se colocaban normalmente en la carpeta de **recursos** con la **acción de compilación: BundleResource**. Sin embargo, este método para trabajar con imágenes en una aplicación de iOS en desuso por Apple. Para obtener más información, vea [tamaños de imagen y nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Adhesión a estas reglas de nomenclatura de los archivos y la colocación permite el XAML cargar y mostrar la imagen en todas las plataformas siguiente:

```xaml
<Image Source="waterfront.jpg" />
```

El código de C# equivalente es como sigue:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Las capturas de pantalla siguientes muestran el resultado de mostrar una imagen local en cada plataforma:

[![aplicación de ejemplo que muestra una imagen local](images-images/local-sml.png)](images-images/local.png#lightbox)

Para mayor flexibilidad, se puede usar la propiedad `Device.RuntimePlatform` para seleccionar un archivo de imagen o una ruta de acceso diferentes para algunas plataformas o todas ellas, como se muestra en este ejemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android 
                ? ImageSource.FromFile("waterfront.jpg") 
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar el mismo nombre de archivo de imagen en todas las plataformas, el nombre debe ser válido en todas las plataformas. Recursos drawable Android tiene restricciones de nomenclatura: se permiten solo letras minúsculas, números, caracteres de subrayado y período: y para la compatibilidad multiplataforma esto debe seguirse en todas las plataformas demasiado. El nombre de archivo **litoral. png** de ejemplo sigue las reglas, pero los ejemplos de nombres de archivo no válidos incluyen "Water Front. png", "litoral. png", "Water-Front. png" y "wåterfront. png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluciones nativas (retina y alta PPP)

iOS, Android y UWP incluyen compatibilidad con resoluciones de imagen diferente, donde el sistema operativo elige la imagen adecuada en tiempo de ejecución según las capacidades del dispositivo. Xamarin.Forms usa las API de las plataformas nativas para cargar imágenes locales, por lo que admite automáticamente las soluciones alternativas si los archivos correctamente se denomina y ubicados en el proyecto.

El método preferido para administrar imágenes desde iOS 9 es arrastrar imágenes para cada resolución necesaria para el conjunto de imágenes del catálogo de activos correspondiente. Para obtener más información, consulte [Agregar imágenes a un conjunto de imágenes del catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes de iOS 9, las versiones de retina de la imagen podrían colocarse en la carpeta de **recursos** : dos y tres veces la resolución con un **@2x** o sufijos de **@3x** en el nombre de archivo antes de la extensión de archivo (por ejemplo, **myimage@2x.png** ). Sin embargo, este método para trabajar con imágenes en una aplicación de iOS en desuso por Apple. Para obtener más información, vea [tamaños de imagen y nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Las imágenes de resolución alternativas de Android deben colocarse en [directorios con nombre especial](https://developer.android.com/guide/practices/screens_support.html) en el proyecto de Android, tal como se muestra en la siguiente captura de pantalla:

[![ubicación de la imagen de varias resoluciones de Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Los nombres de los archivos [de imagen de UWP se pueden incluir en el sufijo `.scale-xxx` antes de la extensión de archivo](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), donde `xxx` es el porcentaje de escalado aplicado al activo, por ejemplo, Scale-200. **PNG**. A continuación, se puede hacer referencia a las imágenes en código o XAML sin el modificador de escala, por ejemplo, simplemente **Image. png**. La plataforma seleccionará la escala de activos correspondiente más cercana en función de PPP de la pantalla actual.

### <a name="additional-controls-that-display-images"></a>Controles adicionales que muestran imágenes

Algunos controles tienen propiedades que se muestran una imagen, como:

- [`Button`](xref:Xamarin.Forms.Button) tiene una propiedad [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) que se puede establecer en una imagen de mapa de bits que se va a mostrar en el `Button`. Para obtener más información, vea [usar mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button) tiene una propiedad [`Source`](xref:Xamarin.Forms.ImageButton.Source) que se puede establecer en la imagen que se va a mostrar en el `ImageButton`. Para obtener más información, vea [establecer el origen de la imagen](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) tiene una propiedad [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) que se puede establecer en una imagen que se carga desde un archivo, un recurso incrustado, un URI o un flujo.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) tiene una propiedad [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) que se puede establecer en una imagen recuperada de un archivo, un recurso incrustado, un URI o un flujo.
- [`Page`](xref:Xamarin.Forms.Page). Cualquier tipo de página que se deriva de `Page` tiene [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) y [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) propiedades, a las que se puede asignar un archivo, un recurso incrustado, un URI o un flujo. En determinadas circunstancias, como cuando un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) muestra un [`ContentPage`](xref:Xamarin.Forms.ContentPage), el icono se mostrará si es compatible con la plataforma.

  > [!IMPORTANT]
  > En iOS, la propiedad [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) no se puede rellenar a partir de una imagen en un conjunto de imágenes del catálogo de recursos. En su lugar, cargue imágenes de iconos para la propiedad `Page.IconImageSource` de un archivo, un recurso incrustado, un URI o un flujo.

## <a name="embedded-images"></a>Imágenes incrustadas

También se incluyen imágenes incrustadas con una aplicación (por ejemplo, imágenes locales), pero en lugar de tener una copia de la imagen en la estructura de archivos de la aplicación la imagen de archivo está incrustado en el ensamblado como un recurso. Este método para distribuir las imágenes se recomienda cuando se usan imágenes idénticas en cada plataforma y es especialmente adecuado para la creación de componentes, como la imagen se incluye con el código.

Para incrustar una imagen en un proyecto, haga doble clic para agregar nuevos elementos y seleccione la imagen/s que desea agregar. De forma predeterminada, la imagen tendrá la **acción de compilación: ninguno**; debe establecerse en acción de **compilación: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![establecer acción de compilación en recurso incrustado](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

La **acción de compilación** se puede ver y cambiar en la ventana **propiedades** de un archivo.

En este ejemplo, el identificador de recurso es **WorkingWithImages. Beach. jpg**.
El IDE ha generado este valor predeterminado mediante la concatenación del **espacio de nombres predeterminado** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

También se puede ver y cambiar la **acción de compilación** en el panel de **propiedades** de un archivo.
Este panel muestra el **identificador de recurso** que se usa para hacer referencia al recurso en el código. En la captura de pantalla siguiente, el **identificador de recurso** es **WorkingWithImages. Beach. jpg**.
El IDE ha generado este valor predeterminado mediante la concatenación del **espacio de nombres predeterminado** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
Este identificador se puede editar en el panel de **propiedades** , pero en estos ejemplos se usará el valor **WorkingWithImages. Beach. jpg** .

[panel de propiedades de recursos incrustados de ![](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Si coloca las imágenes incrustadas en carpetas dentro de su proyecto, los nombres de carpeta también están separados por puntos (.) en el identificador de recurso. Al mover la imagen de **Beach. jpg** a una carpeta llamada mis **imágenes** , se produciría un identificador de recurso de **WorkingWithImages. images. Beach. jpg.**

El código para cargar una imagen incrustada simplemente pasa el **identificador de recurso** al método [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) como se muestra a continuación:

```csharp
var embeddedImage = new Image { 
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg", 
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Para admitir la visualización de imágenes incrustadas en modo de versión en el Plataforma universal de Windows, es necesario usar la sobrecarga de `ImageSource.FromResource` que especifica el ensamblado de origen en el que se va a buscar la imagen.

Actualmente no hay ninguna conversión implícita de los identificadores de recursos. En su lugar, debe usar [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) o `new ResourceImageSource()` para cargar imágenes incrustadas.

Las capturas de pantalla siguientes muestran el resultado de mostrar una imagen incrustada en cada plataforma:

[![aplicación de ejemplo que muestra una imagen incrustada](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Dado que no hay ningún convertidor de tipos integrado de `string` a `ResourceImageSource`, XAML no puede cargar de forma nativa estos tipos de imágenes. En su lugar, se puede escribir una extensión de marcado XAML personalizada simple para cargar imágenes mediante un **identificador de recurso** especificado en XAML:

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> Para admitir la visualización de imágenes incrustadas en modo de versión en el Plataforma universal de Windows, es necesario usar la sobrecarga de `ImageSource.FromResource` que especifica el ensamblado de origen en el que se va a buscar la imagen.

Para usar esta extensión, agregue un `xmlns` personalizado a XAML, con los valores de espacio de nombres y ensamblado correctos para el proyecto. Después, el origen de la imagen se puede establecer mediante esta sintaxis: `{local:ImageResource WorkingWithImages.beach.jpg}`. Un ejemplo completo de XAML se muestra a continuación:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>Solucionar problemas de imágenes incrustadas

#### <a name="debug-code"></a>Depurar código

Dado que a veces es difícil de entender por qué no se puede cargar un recurso de imagen en particular, el siguiente código de depuración se puede agregar temporalmente a una aplicación para ayudarle a confirmar que los recursos están configurados correctamente. Generará todos los recursos conocidos insertados en el ensamblado especificado en la **consola** de para ayudar a depurar los problemas de carga de recursos.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Imágenes incrustadas en otros proyectos

De forma predeterminada, el método `ImageSource.FromResource` solo busca imágenes en el mismo ensamblado que el código que llama al método `ImageSource.FromResource`. Con el código de depuración anterior, puede determinar qué ensamblados contienen un recurso específico cambiando la instrucción `typeof()` a una `Type` que se sabe que está en cada ensamblado.

Sin embargo, el ensamblado de origen en el que se busca una imagen incrustada se puede especificar como argumento para el método `ImageSource.FromResource`:

```csharp
var imageSource = ImageSource.FromResource("filename.png", 
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Descargar imágenes

Las imágenes se pueden descargar automáticamente para su presentación, como se muestra en el XAML siguiente:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

El código de C# equivalente es como sigue:

```csharp
var webImage = new Image { 
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

El método [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) requiere un objeto `Uri` y devuelve un nuevo [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) que lee del `Uri`.

También hay una conversión implícita de cadenas URI, por lo que también funcionará en el ejemplo siguiente:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Las capturas de pantalla siguientes muestran el resultado de mostrar una imagen remota en cada plataforma:

[![aplicación de ejemplo que muestra una imagen descargada](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Almacenamiento en caché de imágenes descargado

Un [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) también admite el almacenamiento en caché de imágenes descargadas, configuradas mediante las siguientes propiedades:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) : Si está habilitado el almacenamiento en caché (`true` de forma predeterminada).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) : un `TimeSpan` que define cuánto tiempo se almacenará la imagen localmente.

Almacenamiento en caché está habilitado de forma predeterminada y almacenará la imagen localmente durante 24 horas. Para deshabilitar el almacenamiento en caché para una imagen concreta, crear una instancia del origen de la imagen como sigue:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

Para establecer un período de caché específica (por ejemplo, 5 días) crear una instancia del origen de la imagen como sigue:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Almacenamiento en caché integrado facilita enormemente admitir escenarios como el desplazamiento de las listas de imágenes, donde puede establecer (o enlazar) una imagen en cada celda y dejar que la memoria caché integrada se encargue de volver a cargar la imagen cuando se desplaza por la celda en la vista.

## <a name="animated-gifs"></a>Archivos GIF animados

Xamarin. Forms incluye compatibilidad para mostrar pequeños GIF animados. Esto se logra estableciendo la propiedad [`Image.Source`](xref:Xamarin.Forms.Image.Source) en un archivo GIF animado:

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Aunque la compatibilidad con los GIF animados de Xamarin. Forms incluye la capacidad de descargar archivos, no admite el almacenamiento en caché ni los GIF animados de streaming.

De forma predeterminada, cuando se carga un GIF animado, no se reproducirá. Esto se debe a que la propiedad `IsAnimationPlaying`, que controla si un GIF animado se está reproduciendo o se detiene, tiene un valor predeterminado de `false`. Esta propiedad, de tipo `bool`, está respaldada por un objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que puede ser el destino de un enlace de datos y con estilo.

Por lo tanto, cuando se carga un GIF animado, no se reproducirá hasta que la propiedad `IsAnimationPlaying` esté establecida en `true`. A continuación, la reproducción se puede detener estableciendo la propiedad `IsAnimationPlaying` en `false`. Tenga en cuenta que esta propiedad no tiene ningún efecto al mostrar un origen de imagen no GIF.

> [!NOTE]
> En Android, la compatibilidad con GIF animados requiere que la aplicación use representadores rápidos y no funcionará si ha optado por el uso de los representadores heredados.
> En UWP, la compatibilidad con GIF animados requiere una versión mínima de la actualización de aniversario de Windows 10 (versión 1607).

## <a name="icons-and-splash-screens"></a>Iconos y pantallas de presentación

Aunque no está relacionado con la vista [`Image`](xref:Xamarin.Forms.Image) , los iconos de la aplicación y las pantallas de presentación también son un uso importante de las imágenes en los proyectos de Xamarin. Forms.

Establecer iconos y pantallas de presentación de las aplicaciones de Xamarin.Forms se realiza en cada uno de los proyectos de aplicación. Esto significa generar correctamente un tamaño de imágenes para iOS, Android y UWP. Estas imágenes deben ser llamadas y encuentra según los requisitos de cada las plataformas.

## <a name="icons"></a>Iconos

Para obtener más información sobre la creación de estos recursos de aplicación, consulte las directrices sobre [Cómo trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md), [Google iconografía](https://developer.android.com/design/style/iconography.html)y [UWP para los activos de iconos e iconos](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

Además, los iconos de fuente se pueden mostrar en la vista [`Image`](xref:Xamarin.Forms.Image) especificando los datos del icono de fuente en un objeto `FontImageSource`. Para obtener más información, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) en la guía de [fuentes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Pantallas de presentación

Solo aplicaciones de iOS y UWP requieren una pantalla de presentación (también denominada una imagen de pantalla o el valor predeterminado de inicio).

Consulte la documentación de [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) y [pantallas de presentación](/windows/uwp/launch-resume/splash-screens/) en el centro de desarrollo de Windows.

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithImages (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografía de Android](https://developer.android.com/design/style/iconography.html)
- [Directrices para los activos de iconos e iconos](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
