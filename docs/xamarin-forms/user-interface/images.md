---
title: Imágenes en Xamarin.Forms
description: Las imágenes se pueden compartir entre plataformas con Xamarin.Forms , se pueden cargar específicamente para cada plataforma o se pueden descargar para su visualización.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 824d5ca711495c8a8ad663034e77506468efd397
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556196"
---
# <a name="images-in-no-locxamarinforms"></a>Imágenes en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Las imágenes se pueden compartir entre plataformas con Xamarin.Forms , se pueden cargar específicamente para cada plataforma o se pueden descargar para su visualización._

Las imágenes son una parte fundamental de la navegación, la facilidad de uso y la personalización de la aplicación. Xamarin.Forms las aplicaciones deben poder compartir imágenes en todas las plataformas, pero también pueden mostrar imágenes diferentes en cada plataforma.

También se necesitan imágenes específicas de la plataforma para iconos y pantallas de presentación. deben configurarse en cada plataforma.

## <a name="display-images"></a>Mostrar imágenes

Xamarin.Forms usa la [`Image`](xref:Xamarin.Forms.Image) vista para mostrar las imágenes en una página. Tiene varias propiedades importantes:

- [`Source`](xref:Xamarin.Forms.Image.Source) : Una [`ImageSource`](xref:Xamarin.Forms.ImageSource) instancia, ya sea archivo, Uri o recurso, que establece la imagen que se va a mostrar.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) : Cómo ajustar el tamaño de la imagen dentro de los límites en los que se muestra (si se va a expandir, recortar o Mostrar en pantalla ancha).

[`ImageSource`](xref:Xamarin.Forms.ImageSource) las instancias se pueden obtener mediante métodos estáticos para cada tipo de origen de imagen:

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -Requiere un nombre de archivo o una ruta de archivo que se pueda resolver en cada plataforma.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) : Requiere un objeto URI, por ejemplo,.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -Requiere un identificador de recursos para un archivo de imagen incrustado en el proyecto de biblioteca o de aplicación de .NET Standard, con una **acción de compilación: EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) : Requiere una secuencia que proporcione datos de imagen.

La [`Aspect`](xref:Xamarin.Forms.Image.Aspect) propiedad determina cómo se escalará la imagen para ajustarse al área de presentación:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) : Estira la imagen para rellenar completamente el área de presentación. Esto puede dar lugar a que la imagen se distorsione.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Recorta la imagen para que rellene el área de visualización conservando el aspecto (es decir, sin distorsión).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) : Muestra una panorámica de la imagen (si es necesario) para que toda la imagen quepa en el área de presentación, con un espacio en blanco agregado a la parte superior o inferior, en función de si la imagen es ancha o alta.

Las imágenes se pueden cargar desde un [archivo local](#local-images), un [recurso incrustado](#embedded-images), [descargado](#download-images)o cargado desde una secuencia. Además, los iconos de fuente se pueden mostrar en la [`Image`](xref:Xamarin.Forms.Image) vista especificando los datos del icono de fuente en un `FontImageSource` objeto. Para obtener más información, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) en la guía de [fuentes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Imágenes locales

Los archivos de imagen se pueden agregar a cada proyecto de aplicación y se puede hacer referencia a ellos desde el Xamarin.Forms código compartido. Este método de distribución de imágenes es necesario cuando las imágenes son específicas de la plataforma, por ejemplo, al usar resoluciones diferentes en plataformas diversas, o bien al emplear diseños que difieren ligeramente.

Para usar una sola imagen en todas las aplicaciones, se *debe usar el mismo nombre de archivo en*todas las plataformas y debe ser un nombre de recurso de Android válido (es decir, solo letras minúsculas, números, el carácter de subrayado y el período permitido).

- **iOS** : la manera preferida de administrar y admitir imágenes ya que Ios 9 es usar **conjuntos de imágenes del catálogo de activos**, que deben contener todas las versiones de una imagen necesarias para admitir varios dispositivos y factores de escala para una aplicación. Para obtener más información, consulte [Agregar imágenes a un conjunto de imágenes del catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** : incluya imágenes en el directorio  **Resources/drawable** con la **acción de compilación: AndroidResource**. También se pueden proporcionar versiones alta y baja de PPP de una imagen (en **los** subdirectorios de recursos con nombre adecuados, como **drawable-ldpi**, **drawable-hdpi**y **drawable-xhdpi**).
- **Plataforma universal de Windows (UWP)** : de forma predeterminada, las imágenes se deben colocar en el directorio raíz de la aplicación con la **acción de compilación: contenido**. Como alternativa, las imágenes se pueden colocar en un directorio diferente que, a continuación, se especifican con una plataforma específica. Para obtener más información, consulte [Directorio de imagen predeterminado en Windows](~/xamarin-forms/platform/windows/default-image-directory.md).

> [!IMPORTANT]
> Antes de iOS 9, las imágenes se colocaban normalmente en la carpeta de **recursos** con la **acción de compilación: BundleResource**. Sin embargo, Apple ha dejado de usar este método de trabajo con imágenes en una aplicación iOS. Para obtener más información, vea [tamaños de imagen y nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

La compatibilidad con estas reglas para la selección de nombres y la ubicación de los archivos permite que el código XAML siguiente cargue y muestre la imagen en todas las plataformas:

```xaml
<Image Source="waterfront.jpg" />
```

El código de C# equivalente es el siguiente:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Las siguientes capturas de pantallas muestran el resultado de mostrar una imagen local en cada plataforma:

[![Aplicación de ejemplo que muestra una imagen local](images-images/local-sml.png)](images-images/local.png#lightbox)

Para mayor flexibilidad `Device.RuntimePlatform` , se puede usar la propiedad para seleccionar un archivo de imagen o una ruta de acceso diferentes para algunas plataformas o para todas, como se muestra en este ejemplo de código:

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Para usar el mismo nombre de archivo de imagen en todas las plataformas, el nombre debe ser válido en todas las plataformas. Los drawabless de Android tienen restricciones de nomenclatura, solo se permiten letras minúsculas, números, caracteres de subrayado y período, y para la compatibilidad multiplataforma, debe seguir también en todas las demás plataformas. El nombre de archivo de ejemplo **waterfront.png** sigue las reglas, pero los ejemplos de nombres de archivo no válidos incluyen "Water front.png", "WaterFront.png", "water-front.png" y "wåterfront.png".

### <a name="native-resolutions-retina-and-high-dpi"></a>Resoluciones nativas (retina y alta PPP)

iOS, Android y UWP incluyen compatibilidad con distintas resoluciones de imagen, donde el sistema operativo elige la imagen adecuada en tiempo de ejecución en función de las capacidades del dispositivo. Xamarin.Forms usa las API de las plataformas nativas para cargar imágenes locales, por lo que admite automáticamente resoluciones alternativas si los archivos se denominan correctamente y se encuentran en el proyecto.

La mejor manera de administrar imágenes desde iOS 9 es arrastrar imágenes para cada resolución necesaria para el conjunto de imágenes del catálogo de activos adecuado. Para obtener más información, consulte [Agregar imágenes a un conjunto de imágenes del catálogo de recursos](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Antes de iOS 9, las versiones de retina de la imagen podrían colocarse en la carpeta de **recursos** : dos y tres veces la resolución con los **@2x** **@3x** sufijos o en el nombre de archivo antes de la extensión de archivo (por ejemplo, **myimage@2x.png**). Sin embargo, Apple ha dejado de usar este método de trabajo con imágenes en una aplicación iOS. Para obtener más información, vea [tamaños de imagen y nombres de archivo](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Las imágenes de resolución alternativas de Android deben colocarse en [directorios con nombre especial](https://developer.android.com/guide/practices/screens_support.html) en el proyecto de Android, tal como se muestra en la siguiente captura de pantalla:

[![Ubicación de la imagen de varias resoluciones de Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Los nombres de los archivos de imagen [de UWP se pueden agregar con el sufijo `.scale-xxx` anterior a la extensión de archivo](/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), donde `xxx` es el porcentaje de escalado aplicado al activo, por ejemplo, **myimage.scale-200.png**. A continuación, se puede hacer referencia a las imágenes en código o XAML sin el modificador de escala, por ejemplo, simplemente **myimage.png**. La plataforma seleccionará la escala de recursos más cercana adecuada en función de los PPP actuales de la pantalla.

### <a name="additional-controls-that-display-images"></a>Controles adicionales que muestran imágenes

Algunos controles tienen propiedades que muestran una imagen, como:

- [`Button`](xref:Xamarin.Forms.Button) tiene una [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) propiedad que se puede establecer en una imagen de mapa de bits que se va a mostrar en `Button` . Para obtener más información, vea [usar mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button) tiene una [`Source`](xref:Xamarin.Forms.ImageButton.Source) propiedad que se puede establecer en la imagen que se va a mostrar en `ImageButton` . Para obtener más información, vea [establecer el origen de la imagen](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) tiene una [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) propiedad que se puede establecer en una imagen que se carga desde un archivo, un recurso incrustado, un URI o un flujo.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) tiene una [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) propiedad que se puede establecer en una imagen recuperada de un archivo, un recurso incrustado, un URI o un flujo.
- [`Page`](xref:Xamarin.Forms.Page). Cualquier tipo de página que derive de `Page` tiene [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) propiedades y, a las que se puede asignar un archivo, un recurso incrustado, un URI o un flujo. En determinadas circunstancias, como cuando [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) se muestra un [`ContentPage`](xref:Xamarin.Forms.ContentPage) , el icono se mostrará si es compatible con la plataforma.

  > [!IMPORTANT]
  > En iOS, la [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propiedad no se puede rellenar a partir de una imagen en un conjunto de imágenes del catálogo de recursos. En su lugar, cargue las imágenes de icono de la `Page.IconImageSource` propiedad desde un archivo, un recurso incrustado, un URI o un flujo.

## <a name="embedded-images"></a>Imágenes incrustadas

Las imágenes incrustadas también se incluyen con una aplicación (como las imágenes locales), pero en lugar de tener una copia de la imagen en la estructura de archivos de cada aplicación, el archivo de imagen se incrusta en el ensamblado como un recurso. Se recomienda este método de distribución de imágenes cuando se usan imágenes idénticas en cada plataforma y es especialmente adecuado para crear componentes, ya que la imagen se incluye en el código.

Para insertar una imagen en un proyecto, haga clic con el botón derecho para agregar nuevos elementos y seleccione la imagen/s que desea agregar. De forma predeterminada, la imagen tendrá la **acción de compilación: ninguno**; debe establecerse en acción de **compilación: EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Establecer acción de compilación en recurso incrustado](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

La **acción de compilación** se puede ver y cambiar en la ventana **propiedades** de un archivo.

En este ejemplo, el identificador de recurso es **WorkingWithImages.beach.jpg**.
El IDE ha generado este valor predeterminado mediante la concatenación del **espacio de nombres predeterminado** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Establecer acción de compilación: EmbeddedResource](images-images/xs-buildaction.png)

También se puede ver y cambiar la **acción de compilación** en el panel de **propiedades** de un archivo.
Este panel muestra el **identificador de recurso** que se usa para hacer referencia al recurso en el código. En la captura de pantalla siguiente, el **identificador de recurso** es **WorkingWithImages.beach.jpg**.
El IDE ha generado este valor predeterminado mediante la concatenación del **espacio de nombres predeterminado** para este proyecto con el nombre de archivo, con un punto (.) entre cada valor.
Este identificador se puede editar en el panel de **propiedades** , pero en estos ejemplos se usará el valor **WorkingWithImages.beach.jpg** .

[![Panel de propiedades de recursos incrustados](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Si coloca imágenes incrustadas en carpetas dentro del proyecto, los nombres de carpeta también se separan por puntos (.) en el identificador de recurso. Al mover la imagen de **beach.jpg** a una carpeta llamada mis **imágenes** , se produciría un identificador de recurso de **WorkingWithImages.MyImages.beach.jpg**

El código para cargar una imagen incrustada simplemente pasa el **identificador de recurso** al [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) método, como se muestra a continuación:

```csharp
Image embeddedImage = new Image
{
    Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(MyClass).GetTypeInfo().Assembly)
};
```

> [!NOTE]
> Para admitir la visualización de imágenes incrustadas en modo de versión en el Plataforma universal de Windows, es necesario usar la sobrecarga de `ImageSource.FromResource` que especifica el ensamblado de origen en el que se va a buscar la imagen.

Actualmente no hay ninguna conversión implícita para los identificadores de recursos. En su lugar, debe usar [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) o `new ResourceImageSource()` para cargar imágenes incrustadas.

Las siguientes capturas de pantallas muestran el resultado de mostrar una imagen incrustada en cada plataforma:

[![Aplicación de ejemplo que muestra una imagen incrustada](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Dado que no hay ningún convertidor de tipos integrado de `string` a `ResourceImageSource` , estos tipos de imágenes no se pueden cargar de forma nativa por XAML. En su lugar, se puede escribir una extensión de marcado XAML personalizada simple para cargar imágenes mediante un **identificador de recurso** especificado en XAML:

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

Para usar esta extensión, agregue un personalizado `xmlns` al XAML, con los valores de espacio de nombres y ensamblado correctos para el proyecto. Después, el origen de la imagen se puede establecer mediante esta sintaxis: `{local:ImageResource WorkingWithImages.beach.jpg}` . A continuación se muestra un ejemplo de XAML completo:

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

Dado que a veces es difícil comprender por qué no se carga un recurso de imagen concreto, se puede agregar temporalmente el siguiente código de depuración a una aplicación para ayudar a confirmar que los recursos están configurados correctamente. Generará todos los recursos conocidos insertados en el ensamblado especificado en la **consola** de para ayudar a depurar los problemas de carga de recursos.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(MyClass).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Imágenes incrustadas en otros proyectos

De forma predeterminada, el `ImageSource.FromResource` método solo busca imágenes en el mismo ensamblado que el código que llama al `ImageSource.FromResource` método. Con el código de depuración anterior, puede determinar qué ensamblados contienen un recurso específico cambiando la `typeof()` instrucción a un `Type` conocido para que esté en cada ensamblado.

Sin embargo, el ensamblado de origen en el que se busca una imagen incrustada se puede especificar como argumento para el `ImageSource.FromResource` método:

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Descargar imágenes

Las imágenes se pueden descargar automáticamente para su presentación, tal como se muestra en el código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://aka.ms/campus.jpg" />
    <Label Text="campus.jpg gets downloaded from microsoft.com" />
  </StackLayout>
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://aka.ms/campus.jpg")
     ) };
```

El [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) método requiere un `Uri` objeto y devuelve un nuevo [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) que lee desde `Uri` .

También hay una conversión implícita de las cadenas URI, por lo que el siguiente ejemplo también funcionará:

```csharp
webImage.Source = "https://aka.ms/campus.jpg";
```

Las siguientes capturas de pantallas muestran el resultado de mostrar una imagen remota en cada plataforma:

[![Aplicación de ejemplo que muestra una imagen descargada](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Almacenamiento en caché de imágenes descargado

[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)También admite el almacenamiento en caché de imágenes descargadas, configuradas mediante las siguientes propiedades:

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) -Si el almacenamiento en caché está habilitado ( `true` de forma predeterminada).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -A `TimeSpan` que define cuánto tiempo se almacenará la imagen localmente.

El almacenamiento en caché está habilitado de forma predeterminada y almacenará la imagen localmente durante 24 horas. Para deshabilitar el almacenamiento en caché de una imagen determinada, cree una instancia del origen de la imagen como se indica a continuación:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("https://server.com/image") };
```

Para establecer un período de caché específico (por ejemplo, 5 días), cree una instancia del origen de la imagen como se indica a continuación:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://aka.ms/campus.jpg"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

El almacenamiento en caché integrado facilita la compatibilidad con escenarios como el desplazamiento de listas de imágenes, donde puede establecer (o enlazar) una imagen en cada celda y dejar que la memoria caché integrada se encargue de volver a cargar la imagen cuando la celda se desplace de nuevo a la vista.

## <a name="animated-gifs"></a>Archivos GIF animados

Xamarin.Forms incluye compatibilidad para mostrar pequeños archivos GIF animados. Esto se logra estableciendo la [`Image.Source`](xref:Xamarin.Forms.Image.Source) propiedad en un archivo GIF animado:

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Aunque la compatibilidad con GIF animado en Xamarin.Forms incluye la capacidad de descargar archivos, no admite el almacenamiento en caché ni los GIF animados de streaming.

De forma predeterminada, cuando se carga un GIF animado, no se reproducirá. Esto se debe `IsAnimationPlaying` a que la propiedad, que controla si un GIF animado se reproduce o se detiene, tiene un valor predeterminado de `false` . Esta propiedad, de tipo `bool` , está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de un enlace de datos y con estilo.

Por lo tanto, cuando se carga un GIF animado, no se reproducirá hasta que la `IsAnimationPlaying` propiedad se establezca en `true` . A continuación, la reproducción se puede detener estableciendo la `IsAnimationPlaying` propiedad en `false` . Tenga en cuenta que esta propiedad no tiene ningún efecto al mostrar un origen de imagen no GIF.

> [!NOTE]
> En Android, la compatibilidad con GIF animados requiere que la aplicación use representadores rápidos y no funcionará si ha optado por el uso de los representadores heredados.
> En UWP, la compatibilidad con GIF animados requiere una versión mínima de la actualización de aniversario de Windows 10 (versión 1607).

## <a name="icons-and-splash-screens"></a>Iconos y pantallas de presentación

Aunque no está relacionado con la [`Image`](xref:Xamarin.Forms.Image) vista, los iconos de la aplicación y las pantallas de presentación también son un uso importante de las imágenes en los Xamarin.Forms proyectos.

La configuración de iconos y pantallas de presentación para Xamarin.Forms aplicaciones se realiza en cada uno de los proyectos de aplicación. Esto significa que se generan imágenes con el tamaño correcto para iOS, Android y UWP. Estas imágenes deben tener un nombre y estar ubicadas según los requisitos de cada plataforma.

## <a name="icons"></a>Iconos

Para obtener más información sobre la creación de estos recursos de aplicación, consulte las directrices sobre [Cómo trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md), [Google iconografía](https://developer.android.com/design/style/iconography.html)y [UWP para los activos de iconos e iconos](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

Además, los iconos de fuente se pueden mostrar en la [`Image`](xref:Xamarin.Forms.Image) vista especificando los datos del icono de fuente en un `FontImageSource` objeto. Para obtener más información, vea [Mostrar iconos de fuentes](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) en la guía de [fuentes](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Pantallas de presentación

Solo las aplicaciones de iOS y UWP requieren una pantalla de presentación (también denominada pantalla de inicio o imagen predeterminada).

Consulte la documentación de [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md) y [pantallas de presentación](/windows/uwp/launch-resume/splash-screens/) en el centro de desarrollo de Windows.

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithImages (ejemplo)](/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS trabajar con imágenes](~/ios/app-fundamentals/images-icons/index.md)
- [Iconografía de Android](https://developer.android.com/design/style/iconography.html)
- [Directrices sobre los activos de iconos y ventanas](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)