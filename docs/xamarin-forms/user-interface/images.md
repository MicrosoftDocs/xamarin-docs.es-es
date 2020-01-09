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
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490191"
---
# <a name="images-in-xamarinforms"></a>Imágenes en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Las imágenes se pueden compartir entre plataformas con Xamarin.Forms, se pueden cargar específicamente para cada plataforma, o se pueden descargar para su presentación._

Las imágenes son una parte fundamental de navegación de la aplicación, la facilidad de uso y la personalización de marca. Las aplicaciones de Xamarin.Forms deben ser capaz de compartir imágenes en todas las plataformas, pero también puede mostrar diferentes imágenes en cada plataforma.

También se requieren para iconos y pantallas de presentación; las imágenes específicas de plataforma estas deben configurarse según el acuerdo con la plataforma.

## <a name="display-images"></a>Mostrar imágenes

Xamarin.Forms usa el [ `Image` ](xref:Xamarin.Forms.Image) vista para mostrar imágenes en una página. Tiene dos propiedades importantes:

- [`Source`](xref:Xamarin.Forms.Image.Source) -Un [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) instancia, archivo, Uri o recurso, que establece la imagen para mostrar.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) -Cómo cambiar el tamaño de la imagen dentro de los límites que se muestra dentro de (ya sea para stretch, recortar o panorámica).

[`ImageSource`](xref:Xamarin.Forms.ImageSource) las instancias pueden obtenerse mediante métodos estáticos para cada tipo de origen de la imagen:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -Requiere un nombre de archivo o ruta del archivo que se puede resolver en cada plataforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -Requiere un objeto Uri, por ejemplo.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -Requiere un identificador de recurso a un archivo de imagen incrustado en la aplicación o el proyecto de biblioteca de .NET Standard con un **EmbeddedResource: acción de compilación**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -Requiere un flujo que proporciona los datos de imagen.

El [ `Aspect` ](xref:Xamarin.Forms.Image.Aspect) propiedad determina cómo la imagen se ajustarán el área de presentación:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -Ajusta la imagen para completamente y exactamente rellenar el área de presentación. Esto puede dar lugar a que la imagen se distorsiona.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Recorta la imagen para que rellene el área de presentación conservando el aspecto (ie. ninguna distorsión).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -Letterbox la imagen (si es necesario) para que quepa la imagen completa en el área de presentación, con espacio en blanco que se agrega a la parte superior o inferior o lados dependiendo de si la imagen es ancho o alto.

Las imágenes se pueden cargar desde un [archivo local](#local-images), un [recurso incrustado](#embedded-images), [descargado](#download-images)o cargado desde una secuencia. Además, los iconos de fuente se pueden mostrar en la vista [`Image`](xref:Xamarin.Forms.Image) especificando los datos del icono de fuente en un objeto `FontImageSource`. Para obtener más información, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) en la guía de [fuentes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Imágenes locales

Archivos de imagen se pueden agregar a cada proyecto de aplicación y hacer referencia desde el código de Xamarin.Forms compartido. Este método de distribución de imágenes es necesario cuando las imágenes son específicas de la plataforma, por ejemplo, al usar resoluciones diferentes en plataformas diversas, o bien al emplear diseños que difieren ligeramente.

Para usar una imagen única en todas las aplicaciones, *se debe usar el mismo nombre de archivo en todas las plataformas*, y debe ser un nombre de recurso de Android válido (es decir. se permiten solo letras minúsculas, números, el carácter de subrayado y el período).

- **iOS** : la preferida en forma de administrar y admitir imágenes, ya que es usar iOS 9 **conjuntos de imágenes del catálogo de activos**, que debe contener todas las versiones de una imagen que son necesarias para admitir varios dispositivos y factores de escala de un aplicación. Para obtener más información, consulte [agregar imágenes a un conjunto de imágenes de catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -colocar imágenes en el **recursos/drawable** directorio con **acción de compilación: AndroidResource**. También se pueden proporcionar versiones de alta y baja resolución de una imagen (en un nombre apropiado **recursos** subdirectorios como **drawable ldpi**, **drawable-hdpi**y **drawable xhdpi**).
- **Plataforma universal de Windows (UWP)** -colocar imágenes en el directorio raíz de la aplicación con **acción de compilación: contenido**.

> [!IMPORTANT]
> Antes de iOS 9, las imágenes normalmente se colocaron en el **recursos** carpeta con **acción de compilación: BundleResource**. Sin embargo, este método para trabajar con imágenes en una aplicación de iOS en desuso por Apple. Para obtener más información, consulte [tamaños de imagen y nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

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

Para obtener más flexibilidad el `Device.RuntimePlatform` propiedad puede utilizarse para seleccionar un archivo de imagen diferente o la ruta de acceso para algunas o todas las plataformas, como se muestra en este ejemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android 
                ? ImageSource.FromFile("waterfront.jpg") 
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar el mismo nombre de archivo de imagen en todas las plataformas, el nombre debe ser válido en todas las plataformas. Recursos drawable Android tiene restricciones de nomenclatura: se permiten solo letras minúsculas, números, caracteres de subrayado y período: y para la compatibilidad multiplataforma esto debe seguirse en todas las plataformas demasiado. El nombre de archivo de ejemplo **waterfront.png** sigue las reglas, pero algunos ejemplos de nombres de archivo no válidos incluyen "agua front.png", "WaterFront.png", "water-front.png" y "wåterfront.png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluciones nativas (retina y alta PPP)

iOS, Android y UWP incluyen compatibilidad con resoluciones de imagen diferente, donde el sistema operativo elige la imagen adecuada en tiempo de ejecución según las capacidades del dispositivo. Xamarin.Forms usa las API de las plataformas nativas para cargar imágenes locales, por lo que admite automáticamente las soluciones alternativas si los archivos correctamente se denomina y ubicados en el proyecto.

El método preferido para administrar imágenes desde iOS 9 es arrastrar imágenes para cada resolución necesaria para el conjunto de imágenes del catálogo de activos correspondiente. Para obtener más información, consulte [agregar imágenes a un conjunto de imágenes de catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes de iOS 9, las versiones de retina de la imagen podrían colocarse en el **recursos** carpeta - dos y tres veces la resolución con una **@2x** o **@3x** sufijos en el nombre de archivo antes de la extensión de archivo (p ej. **myimage@2x.png** ). Sin embargo, este método para trabajar con imágenes en una aplicación de iOS en desuso por Apple. Para obtener más información, consulte [tamaños de imagen y nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Imágenes de Android resolución alternativo deben colocarse en [directorios especialmente denominada](https://developer.android.com/guide/practices/screens_support.html) en el proyecto Android, como se muestra en la captura de pantalla siguiente:

[![ubicación de la imagen de varias resoluciones de Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Los nombres de archivo de imagen UWP [puede tener el sufijo con `.scale-xxx` antes de la extensión de archivo](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), donde `xxx` es el porcentaje de ajuste de escala aplicado al recurso, por ejemplo, **myimage.scale 200.png**. A continuación, se pueden hacer referencia de las imágenes en el código o XAML sin el modificador de escala, por ejemplo, simplemente **myimage.png**. La plataforma seleccionará la escala de activos correspondiente más cercana en función de PPP de la pantalla actual.

### <a name="additional-controls-that-display-images"></a>Controles adicionales que muestran imágenes

Algunos controles tienen propiedades que se muestran una imagen, como:

- [`Button`](xref:Xamarin.Forms.Button) tiene una propiedad [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) que se puede establecer en una imagen de mapa de bits que se va a mostrar en el `Button`. Para obtener más información, vea [usar mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button) tiene una propiedad [`Source`](xref:Xamarin.Forms.ImageButton.Source) que se puede establecer en la imagen que se va a mostrar en el `ImageButton`. Para obtener más información, vea [establecer el origen de la imagen](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) tiene una propiedad [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) que se puede establecer en una imagen que se carga desde un archivo, un recurso incrustado, un URI o un flujo.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) tiene una propiedad [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) que se puede establecer en una imagen recuperada de un archivo, un recurso incrustado, un URI o un flujo.
- [`Page`](xref:Xamarin.Forms.Page). Cualquier tipo de página que se deriva de `Page` tiene [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) y [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) propiedades, a las que se puede asignar un archivo, un recurso incrustado, un URI o un flujo. En determinadas circunstancias, como cuando un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) está mostrando un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), si es compatible con la plataforma, se mostrará el icono.

  > [!IMPORTANT]
  > En iOS, el [ `Page.IconImageSource` ](xref:Xamarin.Forms.Page.IconImageSource) no se pueden rellenar la propiedad desde una imagen en un conjunto de imágenes del catálogo activo. En su lugar, cargue imágenes de iconos para la propiedad `Page.IconImageSource` de un archivo, un recurso incrustado, un URI o un flujo.

## <a name="embedded-images"></a>Imágenes incrustadas

También se incluyen imágenes incrustadas con una aplicación (por ejemplo, imágenes locales), pero en lugar de tener una copia de la imagen en la estructura de archivos de la aplicación la imagen de archivo está incrustado en el ensamblado como un recurso. Este método para distribuir las imágenes se recomienda cuando se usan imágenes idénticas en cada plataforma y es especialmente adecuado para la creación de componentes, como la imagen se incluye con el código.

Para incrustar una imagen en un proyecto, haga doble clic para agregar nuevos elementos y seleccione la imagen/s que desea agregar. De forma predeterminada la imagen tendrá **acción de compilación: Ninguno**; Esto se debe establecerse en **acción de compilación: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![establecer acción de compilación en recurso incrustado](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

El **acción de compilación** se pueden ver y cambiar en el **propiedades** ventana para un archivo.

En este ejemplo es el identificador de recurso **WorkingWithImages.beach.jpg**.
El IDE ha generado este comportamiento predeterminado mediante la concatenación del **predeterminada Namespace** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

**Acción de compilación** también se pueden ver y cambiar en el **propiedades** panel para un archivo.
Este panel muestra la **Id. de recurso** que se utiliza para hacer referencia al recurso en el código. En la captura de pantalla siguiente, la **Id. de recurso** es **WorkingWithImages.beach.jpg**.
El IDE ha generado este comportamiento predeterminado mediante la concatenación del **predeterminada Namespace** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
Este identificador se puede editar en el **propiedades** panel, pero para estos ejemplos el valor **WorkingWithImages.beach.jpg** se usará.

[panel de propiedades de recursos incrustados de ![](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Si coloca las imágenes incrustadas en carpetas dentro de su proyecto, los nombres de carpeta también están separados por puntos (.) en el identificador de recurso. Mover el **beach.jpg** la imagen en una carpeta denominada **MyImages** daría lugar a un identificador de recurso de **WorkingWithImages.MyImages.beach.jpg**

El código para cargar una imagen incrustada simplemente pasa el **Id. de recurso** a la [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*) método tal y como se muestra a continuación:

```csharp
var embeddedImage = new Image { 
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg", 
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Para admitir mostrar imágenes incrustadas en modo de versión en la plataforma Universal de Windows, es necesario utilizar la sobrecarga de `ImageSource.FromResource` que especifica el ensamblado de origen en el que se va a buscar la imagen.

Actualmente no hay ninguna conversión implícita de los identificadores de recursos. En su lugar, debe usar [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*) o `new ResourceImageSource()` para cargar imágenes incrustadas.

Las capturas de pantalla siguientes muestran el resultado de mostrar una imagen incrustada en cada plataforma:

[![aplicación de ejemplo que muestra una imagen incrustada](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Porque no hay ningún convertidor de tipos integrados de `string` a `ResourceImageSource`, estos tipos de imágenes no se puede cargar de forma nativa por XAML. En su lugar, se puede escribir una extensión de marcado XAML personalizada simple para cargar imágenes mediante un **Id. de recurso** especificado en XAML:

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
> Para admitir mostrar imágenes incrustadas en modo de versión en la plataforma Universal de Windows, es necesario utilizar la sobrecarga de `ImageSource.FromResource` que especifica el ensamblado de origen en el que se va a buscar la imagen.

Para usar esta extensión agregar personalizada `xmlns` para el XAML, con los valores de espacio de nombres y ensamblado correctos para el proyecto. A continuación, se puede establecer el origen de imagen con esta sintaxis: `{local:ImageResource WorkingWithImages.beach.jpg}`. Un ejemplo completo de XAML se muestra a continuación:

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

Dado que a veces es difícil de entender por qué no se puede cargar un recurso de imagen en particular, el siguiente código de depuración se puede agregar temporalmente a una aplicación para ayudarle a confirmar que los recursos están configurados correctamente. Dará como resultado conocidos todos los recursos incrustados en el ensamblado especificado para el **consola** para ayudar a depurar problemas de carga de recursos.

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

De forma predeterminada, el `ImageSource.FromResource` método busca solo las imágenes en el mismo ensamblado que el código que llama el `ImageSource.FromResource` método. Con el código de depuración posteriores puede determinar qué ensamblados contienen un recurso específico cambiando el `typeof()` instrucción a una `Type` sabe que en cada ensamblado.

Sin embargo, se puede especificar el ensamblado de origen que se va a buscar una imagen incrustada como un argumento para el `ImageSource.FromResource` método:

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

El [ `ImageSource.FromUri` ](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) método requiere un `Uri` de objetos y devuelve un nuevo [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource) que lee el `Uri`.

También hay una conversión implícita de cadenas URI, por lo que también funcionará en el ejemplo siguiente:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Las capturas de pantalla siguientes muestran el resultado de mostrar una imagen remota en cada plataforma:

[![aplicación de ejemplo que muestra una imagen descargada](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Almacenamiento en caché de imágenes descargado

Un [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource) también admite el almacenamiento en caché de imágenes descargadas, configuradas a través de las siguientes propiedades:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) -Si está habilitado el almacenamiento en caché (`true` de forma predeterminada).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -A `TimeSpan` que define cuánto tiempo la imagen se almacenarán localmente.

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

Aunque no se refiere a la [ `Image` ](xref:Xamarin.Forms.Image) vista, los iconos de aplicación y pantallas de presentación también son un uso importante de las imágenes en proyectos de Xamarin.Forms.

Establecer iconos y pantallas de presentación de las aplicaciones de Xamarin.Forms se realiza en cada uno de los proyectos de aplicación. Esto significa generar correctamente un tamaño de imágenes para iOS, Android y UWP. Estas imágenes deben ser llamadas y encuentra según los requisitos de cada las plataformas.

## <a name="icons"></a>Iconos

Para obtener más información sobre la creación de estos recursos de aplicación, consulte las directrices sobre [Cómo trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md), [Google iconografía](https://developer.android.com/design/style/iconography.html)y [UWP para los activos de iconos e iconos](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

Además, los iconos de fuente se pueden mostrar en la vista [`Image`](xref:Xamarin.Forms.Image) especificando los datos del icono de fuente en un objeto `FontImageSource`. Para obtener más información, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) en la guía de [fuentes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Pantallas de presentación

Solo aplicaciones de iOS y UWP requieren una pantalla de presentación (también denominada una imagen de pantalla o el valor predeterminado de inicio).

Consulte la documentación para [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) y [pantallas de presentación](/windows/uwp/launch-resume/splash-screens/) en el centro de desarrollo de Windows.

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithImages (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografía Android](https://developer.android.com/design/style/iconography.html)
- [Directrices para los recursos de icono y el icono](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
