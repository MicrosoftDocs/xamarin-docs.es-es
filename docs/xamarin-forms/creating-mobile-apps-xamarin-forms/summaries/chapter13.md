---
title: Resumen del capítulo 13. Mapas de bits
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen del capítulo 13. Mapas de bits'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725533"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumen del capítulo 13. Mapas de bits

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Notas de esta página indican áreas donde se ha dividido Xamarin.Forms desde el material presentado en el libro.

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image) elemento muestra un mapa de bits. Todas las plataformas de Xamarin.Forms admiten los formatos de archivo JPEG, PNG, GIF y BMP.

Mapas de bits en Xamarin.Forms proceden de cuatro lugares:

- A través de la web tal como se especifica mediante una dirección URL
- Incrustado como un recurso en la biblioteca compartida
- Incrustado como un recurso en los proyectos de aplicación de plataforma
- Desde cualquier lugar que pueden hacer referencia a .NET `Stream` objeto, incluidos `MemoryStream`

Recursos de mapa de bits en la biblioteca compartida son independientes de la plataforma, mientras que los recursos de mapa de bits en los proyectos de plataforma son específicos de la plataforma.

> [!NOTE]
> El texto del libro hace referencia a bibliotecas de clases portables, que se han reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares.

El mapa de bits se especifica estableciendo el [ `Source` ](xref:Xamarin.Forms.Image.Source) propiedad de `Image` a un objeto de tipo [ `ImageSource` ](xref:Xamarin.Forms.ImageSource), una clase abstracta con tres derivados:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) Para obtener acceso a un mapa de bits a través de web según un `Uri` objeto establecido en su [ `Uri` ](xref:Xamarin.Forms.UriImageSource.Uri) propiedad
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) Para obtener acceso a un mapa de bits almacenado en un proyecto de aplicación de plataforma basada en una ruta de carpeta y archivo establecida en su [ `File` ](xref:Xamarin.Forms.FileImageSource.File) propiedad
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) para cargar un mapa de bits mediante .NET `Stream` objeto especificado al devolver un `Stream` desde un `Func` establecido en su [ `Stream` ](xref:Xamarin.Forms.StreamImageSource.Stream) propiedad

Como alternativa (y más comúnmente) puede usar los siguientes métodos estáticos de la `ImageSource` clase, todos devuelven `ImageSource` objetos:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) Para obtener acceso a un mapa de bits a través de web según un `Uri` objeto
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) Para obtener acceso a un mapa de bits que se almacenan como un recurso incrustado en la aplicación PCL; [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) o [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para tener acceso a un mapa de bits en otro ensamblado de origen
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) Para obtener acceso a un mapa de bits desde un proyecto de aplicación de plataforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) para cargar un mapa de bits en función de un `Stream` objeto

No hay ningún equivalente de la clase de la `Image.FromResource` métodos. La `UriImageSource` clase es útil si necesita controlar el almacenamiento en caché. La `FileImageSource` clase es útil en XAML. `StreamImageSource` es útil para la carga asincrónica de `Stream` objetos, mientras que `ImageSource.FromStream` es sincrónica.

## <a name="platform-independent-bitmaps"></a>Mapas de bits independiente de la plataforma

El [ **WebBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) proyecto carga un mapa de bits a través de web mediante `ImageSource.FromUri`. El `Image` elemento está establecido en el `Content` propiedad de la `ContentPage`, por lo que está restringido para el tamaño de la página. Independientemente del tamaño del mapa de bits, un restringida `Image` elemento se ajusta al tamaño de su contenedor y se muestra el mapa de bits en su tamaño máximo en el `Image` elemento manteniendo la proporción de aspecto del mapa de bits. Áreas de la `Image` más allá de lo que se puede colorear el mapa de bits con [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor).

El [ **WebBitmapXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) ejemplo es similar pero simplemente establece la `Source` propiedad a la dirección URL. La conversión se controla mediante el [ `ImageSourceConverter` ](xref:Xamarin.Forms.ImageSourceConverter) clase.

### <a name="fit-and-fill"></a>Ajuste y relleno

Puede controlar cómo se ajusta el mapa de bits estableciendo el [ `Aspect` ](xref:Xamarin.Forms.Image.Aspect) propiedad de la `Image` a uno de los siguientes miembros de la [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeración:

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): respeta la relación de aspecto (valor predeterminado)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): rellena el área, no respeta la relación de aspecto
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): rellena el área pero respeta la relación de aspecto, consigue recortar parte del mapa de bits

### <a name="embedded-resources"></a>Recursos incrustados

Puede agregar un archivo de mapa de bits a una PCL o a una carpeta en la PCL. Asígnele un **acción de compilación** de **EmbeddedResource**. El [ **ResourceBitmapCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) ejemplo muestra cómo usar `ImageSource.FromResource` para cargar el archivo. El nombre de recurso que se pasa al método consta del nombre del ensamblado, seguido por un punto, seguido del nombre de carpeta opcional y un punto, seguido por el nombre de archivo.

El programa se establece la `VerticalOptions` y `HorizontalOptions` propiedades de la `Image` a `LayoutOptions.Center`, lo que hace el `Image` elemento sin restricciones. El `Image` y el mapa de bits tienen el mismo tamaño:

- En iOS y Android, el `Image` es el tamaño de píxel del mapa de bits. Hay una asignación unívoca entre los píxeles del mapa de bits y pantalla.
- En la plataforma Universal de Windows, el `Image` es el tamaño de píxel del mapa de bits en unidades independientes del dispositivo. En la mayoría de los dispositivos, cada píxel del mapa de bits ocupa varias píxeles de la pantalla.

El [ **StackedBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) ejemplo pone un `Image` en una vertical `StackLayout` en XAML. Una extensión de marcado denominada [ `ImageResourceExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ayuda a hacer referencia al recurso incrustado en XAML. Esta clase solo carga los recursos del ensamblado donde se encuentra, por lo que no se coloca en una biblioteca.

### <a name="more-on-sizing"></a>Más información sobre el ajuste de tamaño

Suele ser deseable en mapas de bits de tamaño coherente entre todas las plataformas.
Experimentar con **StackedBitmap**, puede establecer un `WidthRequest` en el `Image` elemento en una vertical `StackLayout` para que el tamaño coherentes entre las plataformas, pero solo puede reducir el tamaño mediante esta técnica.

También puede establecer el `HeightRequest` garantizar la imagen tamaños coherente en las plataformas, pero el ancho del mapa de bits restringido, se limitará la versatilidad de esta técnica. Para una imagen en una vertical `StackLayout`, `HeightRequest` debe evitarse.

Lo mejor es comenzar con un mapa de bits mayor que el ancho del teléfono en unidades independientes del dispositivo y establecer `WidthRequest` ancho deseado en unidades independientes del dispositivo. Esto se muestra en el [ **DeviceIndBitmapSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) ejemplo.

El [ **MadTeaParty** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) muestra capítulo 7 del de Lewis Carroll *aventuras de Alicia en el país de las maravillas* con las ilustraciones por John Tenniel originales:

[![Captura de pantalla triple de la fiesta de té de Mad](images/ch13fg16-small.png "Texto del libro de fiesta de té Hatters de Mad")](images/ch13fg16-large.png#lightbox "Texto del libro de fiesta de té Hatters de Mad")

### <a name="browsing-and-waiting"></a>Exploración y en espera

El [ **ImageBrowser** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) ejemplo permite al usuario explorar a través de varias imágenes existentes almacenadas en el sitio web de Xamarin. Usa .NET [ `WebRequest` ](xref:System.Net.WebRequest) clase para descargar un archivo JSON con la lista de mapas de bits.

> [!NOTE]
> Deben usar programas de Xamarin.Forms [ `HttpClient` ](xref:System.Net.Http.HttpClient) lugar [ `WebRequest` ](xref:System.Net.WebRequest) para tener acceso a archivos a través de internet.

El programa usa un [ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) para indicar que algo está ocurriendo. Ya se está cargando cada mapa de bits, solo lectura [ `IsLoading` ](xref:Xamarin.Forms.Image.IsLoading) propiedad de `Image` es `true`. El `IsLoading` propiedad está respaldada por una propiedad enlazable, por lo tanto un `PropertyChanged` evento se desencadena cuando se cambia dicha propiedad. El programa se adjunta un controlador a este evento y usa el valor actual de `IsLoaded` para establecer el [ `IsRunning` ](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propiedad de la `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Los mapas de bits de streaming

El `ImageSource.FromStream` método crea un `ImageSource` basado en .NET `Stream`. El método se debe pasar un `Func` objeto que devuelve un `Stream` objeto.

### <a name="accessing-the-streams"></a>Obtener acceso a los flujos

El [ **BitmapStreams** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) ejemplo muestra cómo usar el `ImaageSource.FromStream` método para cargar un mapa de bits que se almacenan como un recurso incrustado y la carga de un mapa de bits en la web.

### <a name="generating-bitmaps-at-run-time"></a>Generar mapas de bits en tiempo de ejecución

Todas las plataformas de Xamarin.Forms admiten el formato de archivo BMP sin comprimir, que es fácil de construcción de código y, a continuación, almacenar en un `MemoryStream`. Esta técnica permite de forma algorítmica crear mapas de bits en tiempo de ejecución, tal como se implementa en el [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) clase en el **Xamrin.FormsBook.Toolkit** biblioteca.

El "Do It Yourself" [ **DiyGradientBitmap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) ejemplo muestra el uso de `BmpMaker` para crear un mapa de bits con una imagen de degradado.

## <a name="platform-specific-bitmaps"></a>Mapas de bits específicos de la plataforma

Todas las plataformas de Xamarin.Forms permiten almacenar mapas de bits en los ensamblados de aplicación de plataforma. Cuando se recuperan mediante una aplicación de Xamarin.Forms, estos mapas de bits de la plataforma son de tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource). Se usan para:

- el [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) propiedad de [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- el [ `Icon` ](xref:Xamarin.Forms.MenuItem.Icon) propiedad de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- el [ `Image` ](xref:Xamarin.Forms.Button) propiedad de `Button`

Los ensamblados de plataforma ya contienen los mapas de bits para iconos y pantallas de presentación:

- En el proyecto de iOS, en el **recursos** carpeta
- En el proyecto Android, en las subcarpetas de la **recursos** carpeta
- En los proyectos de Windows, en el **activos** carpeta (aunque las plataformas de Windows no restringen los mapas de bits a la carpeta)

El [ **PlatformBitmaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) ejemplo usa código para mostrar un icono de los proyectos de aplicación de plataforma.

### <a name="bitmap-resolutions"></a>Resoluciones de mapa de bits

Todas las plataformas permiten almacenar varias versiones de imágenes de mapa de bits para las resoluciones de dispositivo diferentes. En tiempo de ejecución, la versión correcta se carga en función de la resolución del dispositivo de la pantalla.

En iOS, estos mapas de bits se diferencian por un sufijo en el nombre de archivo:

- Sin sufijo para dispositivos de 160 PPP (de 1 píxel en la unidad independiente del dispositivo)
- '@2x' sufijo para dispositivos de 320 PPP (2 píxeles a la DIU)
- '@3x' sufijo para dispositivos de 480 PPP (3 píxeles a la DIU)

Un mapa de bits diseñado para mostrarse como una pulgada cuadrada existiría en tres versiones:

- MyImage.jpg de 160 píxeles cuadrados
- MyImage@2x.jpg a 320 píxeles cuadrados
- MyImage@3x.jpg en 480 píxeles cuadrados

El programa debería hacer referencia a este mapa de bits como MyImage.jpg, pero se recupera la versión correcta en tiempo de ejecución basándose en la resolución de la pantalla. Cuando no presentan restricciones, el mapa de bits siempre se representará en 160 unidades independientes del dispositivo.

Para Android, los mapas de bits se almacenan en varias subcarpetas de la **recursos** carpeta:

- drawable-ldpi (una resolución baja) con dispositivos de 120 ppp (0,75 píxeles a la DIU)
- drawable-mdpi (medio) con dispositivos de 160 PPP (de 1 píxel en la DIU)
- drawable-hdpi (alto) con dispositivos de 240 ppp (1,5 píxeles a la DIU)
- drawable-xhdpi (alto) para dispositivos de 320 PPP (2 píxeles a la DIU)
- drawable-xxhdpi (muy alto) para dispositivos de 480 PPP (3 píxeles a la DIU)
- drawable-xxxhdpi (tres puntos altos adicionales) con dispositivos de 640 PPP (4 píxeles para el DIU)

Para un mapa de bits pensado para representarse en una pulgada cuadrada, las distintas versiones del mapa de bits tendrán el mismo nombre pero con un tamaño diferente y se almacenan en estas carpetas:

- drawable-ldpi/MyImage.jpg en 120 píxeles cuadrados
- drawable-mdpi/MyImage.jpg de 160 píxeles cuadrados
- drawable-hdpi/MyImage.jpg en 240 píxeles cuadrados
- drawable-xhdpi/MyImage.jpg a 320 píxeles cuadrados
- drawable-xxhdpi/MyImage.jpg en 480 píxeles cuadrados
- drawable-xxxhdpi/MyImage.jpg en 640 píxeles cuadrados

El mapa de bits siempre se representará en 160 unidades independientes del dispositivo. (La plantilla de solución de Xamarin.Forms estándar solo incluye el hdpi, xhdpi y las carpetas xxhdpi.)

El proyecto de UWP es compatible con un esquema de nomenclatura de mapa de bits que consta de un factor de escala en píxeles independientes del dispositivo unitario como un porcentaje, por ejemplo:

- MyImage.scale-200.jpg a 320 píxeles cuadrados

Solo algunos porcentajes son válidos. Los programas de ejemplo para este libro incluyen solo las imágenes con **escala 200** sufijos, pero con plantillas de solución de Xamarin.Forms actuales incluyen **escala 100**, **escala 125**, **escala 150**, y **escala 400**.

Al agregar mapas de bits a los proyectos de plataforma, el **acción de compilación** debe ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **contenido**

El [ **ImageTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) ejemplo crea dos objetos con apariencia de botón que consta de `Image` elementos con un `TapGestureRecognizer` instalado. Se pretende que los objetos de ser una pulgada cuadrada. El `Source` propiedad de `Image` se establece mediante `OnPlatform` y `On` objetos que se va a hacer referencia a nombres de archivo potencialmente diferentes en las plataformas. Las imágenes de mapa de bits incluyen números que indica su tamaño en píxeles, para que pueda ver el mapa de bits de tamaño se recupera y procesa.

### <a name="toolbars-and-their-icons"></a>Las barras de herramientas y sus iconos

Uno de los usos principales de mapas de bits específicos de la plataforma es la barra de herramientas de Xamarin.Forms, que se construye mediante la adición de [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) objetos a la [ `ToolbarItems` ](xref:Xamarin.Forms.Page.ToolbarItems) colección definida por `Page`. `ToobarItem` se deriva de [ `MenuItem` ](xref:Xamarin.Forms.MenuItem) desde la que se hereda algunas propiedades.

Lo más importante `ToolbarItem` propiedades son:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) para el texto que puede aparecer según la plataforma y `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de tipo `FileImageSource` para la imagen que puede aparecer según la plataforma y `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) de tipo [ `ToolbarItemOrder` ](xref:Xamarin.Forms.ToolbarItemOrder), una enumeración con tres miembros [ `Default` ](xref:Xamarin.Forms.ToolbarItemOrder.Default), [ `Primary` ](xref:Xamarin.Forms.ToolbarItemOrder.Primary), y [ `Secondary` ](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

El número de `Primary` elementos deben limitarse a tres o cuatro. Debe incluir un `Text` establecer para todos los elementos. Para la mayoría de las plataformas, sólo el `Primary` elementos requieren una `Icon` pero requiere Windows 8.1 un `Icon` para todos los elementos. Los iconos deben tener 32 unidades independientes del dispositivo cuadradas. El `FileImageSource` tipo indica que son específicos de la plataforma.

El `ToolbarItem` se activa un [ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked) eventos cuando se puntea muy similar a un `Button`. `ToolbarItem` también admite [ `Command` ](xref:Xamarin.Forms.MenuItem.Command) y [ `CommandParameter` ](xref:Xamarin.Forms.MenuItem.CommandParameter) propiedades que se usan a menudo en relación con MVVM. (Consulte [capítulo 18, MVVM](chapter18.md)).

IOS y Android requieren que sea una página que muestra una barra de herramientas un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) o una página para navegar por un `NavigationPage`. El [ **ToolbarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) programa conjuntos el `MainPage` propiedad de su `App` clase a la [ `NavigationPage` constructor](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) con un `ContentPage` argumento y se muestra la construcción y controlador de eventos de una barra de herramientas.

### <a name="button-images"></a>Imágenes de botón

También puede usar mapas de bits específicos de la plataforma para establecer el [ `Image` ](xref:Xamarin.Forms.Button.Image) propiedad de `Button` un mapa de bits del cuadrado 32 unidades independientes del dispositivo, tal como lo demuestra el [ **ButtonImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) ejemplo.

> [!NOTE]
> Se ha mejorado el uso de imágenes en los botones. Consulte [usando mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 13, texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Ejemplos de capítulo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Working with Images (Trabajo con imágenes)](~/xamarin-forms/user-interface/images.md)
- [Uso de mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
