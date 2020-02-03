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

El elemento [`Image`](xref:Xamarin.Forms.Image) de Xamarin. Forms muestra un mapa de bits. Todas las plataformas de Xamarin.Forms admiten los formatos de archivo JPEG, PNG, GIF y BMP.

Mapas de bits en Xamarin.Forms proceden de cuatro lugares:

- A través de la web tal como se especifica mediante una dirección URL
- Incrustado como un recurso en la biblioteca compartida
- Incrustado como un recurso en los proyectos de aplicación de plataforma
- Desde cualquier lugar al que pueda hacer referencia un objeto de `Stream` .NET, incluido `MemoryStream`

Recursos de mapa de bits en la biblioteca compartida son independientes de la plataforma, mientras que los recursos de mapa de bits en los proyectos de plataforma son específicos de la plataforma.

> [!NOTE]
> El texto del libro hace referencia a bibliotecas de clases portables, que se han reemplazado por las bibliotecas de .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas de .NET estándares.

El mapa de bits se especifica estableciendo la propiedad [`Source`](xref:Xamarin.Forms.Image.Source) de `Image` en un objeto de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), una clase abstracta con tres derivados:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) para tener acceso a un mapa de bits a través de la web basándose en un objeto `Uri` establecido en su propiedad [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) para acceder a un mapa de bits almacenado en un proyecto de aplicación de plataforma basado en una ruta de acceso de archivo y carpeta establecida en su propiedad [`File`](xref:Xamarin.Forms.FileImageSource.File)
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) para cargar un mapa de bits mediante un objeto de `Stream` .net especificado devolviendo un `Stream` de un conjunto de `Func` a su propiedad [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream)

Como alternativa, puede usar los siguientes métodos estáticos de la clase `ImageSource`, que devuelven objetos `ImageSource`:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) para tener acceso a un mapa de bits a través de la web basándose en un objeto `Uri`
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) para tener acceso a un mapa de bits almacenado como un recurso incrustado en la aplicación PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) o [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para tener acceso a un mapa de bits en otro ensamblado de origen
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) para tener acceso a un mapa de bits desde un proyecto de aplicación de plataforma
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) para cargar un mapa de bits basado en un objeto `Stream`

No hay ningún equivalente de clase de los métodos `Image.FromResource`. La clase `UriImageSource` es útil si necesita controlar el almacenamiento en caché. La clase `FileImageSource` es útil en XAML. `StreamImageSource` es útil para la carga asincrónica de objetos `Stream`, mientras que `ImageSource.FromStream` es sincrónico.

## <a name="platform-independent-bitmaps"></a>Mapas de bits independiente de la plataforma

El proyecto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carga un mapa de bits a través de la web mediante `ImageSource.FromUri`. El elemento `Image` se establece en la propiedad `Content` de la `ContentPage`, por lo que se limita al tamaño de la página. Independientemente del tamaño del mapa de bits, un elemento `Image` restringido se ajusta al tamaño de su contenedor y el mapa de bits se muestra en su tamaño máximo dentro del elemento `Image` mientras se mantiene la relación de aspecto del mapa de bits. Las áreas del `Image` más allá del mapa de bits se pueden colorear con [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor).

El ejemplo [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) es similar, pero simplemente establece la propiedad `Source` en la dirección URL. La clase [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) controla la conversión.

### <a name="fit-and-fill"></a>Ajuste y relleno

Puede controlar cómo se ajusta el mapa de bits estableciendo la propiedad [`Aspect`](xref:Xamarin.Forms.Image.Aspect) del `Image` en uno de los siguientes miembros de la enumeración [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): relación de aspecto de los aspectos (valor predeterminado)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): el área rellena, no respeta la relación de aspecto
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): rellena el área pero respeta la relación de aspecto, se consigue recortando parte del mapa de bits

### <a name="embedded-resources"></a>Recursos incrustados

Puede agregar un archivo de mapa de bits a una PCL o a una carpeta en la PCL. Asígnele una **acción de compilación** de **EmbeddedResource**. En el ejemplo [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) se muestra cómo usar `ImageSource.FromResource` para cargar el archivo. El nombre de recurso que se pasa al método consta del nombre del ensamblado, seguido por un punto, seguido del nombre de carpeta opcional y un punto, seguido por el nombre de archivo.

El programa establece las propiedades `VerticalOptions` y `HorizontalOptions` de la `Image` en `LayoutOptions.Center`, lo que hace que el elemento `Image` quede sin restricciones. El `Image` y el mapa de bits tienen el mismo tamaño:

- En iOS y Android, el `Image` es el tamaño de píxel del mapa de bits. Hay una asignación unívoca entre los píxeles del mapa de bits y pantalla.
- En Plataforma universal de Windows, el `Image` es el tamaño en píxeles del mapa de bits en unidades independientes del dispositivo. En la mayoría de los dispositivos, cada píxel del mapa de bits ocupa varias píxeles de la pantalla.

En el ejemplo [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) se coloca una `Image` en un `StackLayout` vertical en XAML. Una extensión de marcado denominada [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ayuda a hacer referencia al recurso incrustado en XAML. Esta clase solo carga los recursos del ensamblado donde se encuentra, por lo que no se coloca en una biblioteca.

### <a name="more-on-sizing"></a>Más información sobre el ajuste de tamaño

Suele ser deseable en mapas de bits de tamaño coherente entre todas las plataformas.
Experimentando con **StackedBitmap**, puede establecer un `WidthRequest` en el elemento `Image` en un `StackLayout` vertical para que el tamaño sea coherente entre las plataformas, pero solo puede reducir el tamaño mediante esta técnica.

También puede establecer la `HeightRequest` para que los tamaños de imagen sean coherentes en las plataformas, pero el ancho restringido del mapa de bits limitará la versatilidad de esta técnica. En el caso de una imagen en un `StackLayout`vertical, se debe evitar `HeightRequest`.

El mejor enfoque es comenzar con un mapa de bits más ancho que el ancho del teléfono en unidades independientes del dispositivo y establecer `WidthRequest` en un ancho deseado en unidades independientes del dispositivo. Esto se muestra en el ejemplo [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

En el [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) se muestra el capítulo 7 de las aventuras de Alicia de Lewis Carroll *en Wonderland* con las ilustraciones originales de John Tenniel:

[![Captura de pantalla triple de la fiesta de té de Mad](images/ch13fg16-small.png "Texto del libro de fiesta de té Hatters de Mad")](images/ch13fg16-large.png#lightbox "Texto del libro de fiesta de té Hatters de Mad")

### <a name="browsing-and-waiting"></a>Exploración y en espera

El ejemplo [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permite al usuario examinar las imágenes de acciones almacenadas en el sitio web de Xamarin. Usa la clase de [`WebRequest`](xref:System.Net.WebRequest) de .net para descargar un archivo JSON con la lista de mapas de bits.

> [!NOTE]
> Los programas de Xamarin. Forms deben usar [`HttpClient`](xref:System.Net.Http.HttpClient) en lugar de [`WebRequest`](xref:System.Net.WebRequest) para tener acceso a los archivos a través de Internet.

El programa usa un [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) para indicar que algo está ocurriendo. A medida que se carga cada mapa de bits, se `true`la propiedad [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) de solo lectura de `Image`. La propiedad `IsLoading` está respaldada por una propiedad enlazable, por lo que se desencadena un evento `PropertyChanged` cuando cambia esa propiedad. El programa adjunta un controlador a este evento y usa la configuración actual de `IsLoaded` para establecer la propiedad [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) de la `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Los mapas de bits de streaming

El método `ImageSource.FromStream` crea un `ImageSource` basado en un `Stream`.NET. Se debe pasar al método un objeto `Func` que devuelva un objeto `Stream`.

### <a name="accessing-the-streams"></a>Obtener acceso a los flujos

En el ejemplo [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) se muestra cómo usar el método `ImaageSource.FromStream` para cargar un mapa de bits almacenado como un recurso incrustado y para cargar un mapa de bits en la Web.

### <a name="generating-bitmaps-at-run-time"></a>Generar mapas de bits en tiempo de ejecución

Todas las plataformas de Xamarin. Forms admiten el formato de archivo BMP sin comprimir, que es fácil de construir en el código y luego almacenar en una `MemoryStream`. Esta técnica permite la creación algorítmica de mapas de bits en tiempo de ejecución, tal y como se implementa en la clase [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) de la biblioteca **Xamrin. FormsBook. Toolkit** .

El ejemplo [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) "hágalo usted" muestra el uso de `BmpMaker` para crear un mapa de bits con una imagen de degradado.

## <a name="platform-specific-bitmaps"></a>Mapas de bits específicos de la plataforma

Todas las plataformas de Xamarin.Forms permiten almacenar mapas de bits en los ensamblados de aplicación de plataforma. Cuando una aplicación de Xamarin. Forms la recupera, estos mapas de bits de plataforma son del tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Se usan para:

- la propiedad [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- la propiedad [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- la propiedad [`Image`](xref:Xamarin.Forms.Button) de `Button`

Los ensamblados de plataforma ya contienen los mapas de bits para iconos y pantallas de presentación:

- En el proyecto de iOS, en la carpeta **recursos**
- En el proyecto de Android, en las subcarpetas de la carpeta **recursos**
- En los proyectos de Windows, en la carpeta **assets** (aunque las plataformas de Windows no restringen los mapas de bits a esa carpeta)

En el ejemplo [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) se usa código para mostrar un icono de los proyectos de aplicación de la plataforma.

### <a name="bitmap-resolutions"></a>Resoluciones de mapa de bits

Todas las plataformas permiten almacenar varias versiones de imágenes de mapa de bits para las resoluciones de dispositivo diferentes. En tiempo de ejecución, la versión correcta se carga en función de la resolución del dispositivo de la pantalla.

En iOS, estos mapas de bits se diferencian por un sufijo en el nombre de archivo:

- Sin sufijo para dispositivos de 160 PPP (de 1 píxel en la unidad independiente del dispositivo)
- sufijo '@2x' para dispositivos 320 PPP (2 píxeles a DIU)
- sufijo '@3x' para dispositivos 480 PPP (3 píxeles a DIU)

Un mapa de bits diseñado para mostrarse como una pulgada cuadrada existiría en tres versiones:

- MyImage.jpg de 160 píxeles cuadrados
- MyImage@2x.jpg en 320 píxeles cuadrados
- MyImage@3x.jpg en 480 píxeles cuadrados

El programa debería hacer referencia a este mapa de bits como MyImage.jpg, pero se recupera la versión correcta en tiempo de ejecución basándose en la resolución de la pantalla. Cuando no presentan restricciones, el mapa de bits siempre se representará en 160 unidades independientes del dispositivo.

En el caso de Android, los mapas de bits se almacenan en varias subcarpetas de la carpeta **recursos** :

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

Solo algunos porcentajes son válidos. Los programas de ejemplo de este libro incluyen solo imágenes con sufijos **de escala 200** , pero las plantillas de solución de Xamarin. Forms actuales incluyen **Scale-100**, **Scale-125**, **Scale-150**y **Scale-400**.

Al agregar mapas de bits a los proyectos de la plataforma, la **acción de compilación** debe ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **contenido**

En el ejemplo [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) se crean dos objetos similares a los botones que se componen de `Image` elementos con un `TapGestureRecognizer` instalado. Se pretende que los objetos de ser una pulgada cuadrada. La propiedad `Source` de `Image` se establece mediante `OnPlatform` y `On` objetos para hacer referencia a nombres de archivo potencialmente diferentes en las plataformas. Las imágenes de mapa de bits incluyen números que indica su tamaño en píxeles, para que pueda ver el mapa de bits de tamaño se recupera y procesa.

### <a name="toolbars-and-their-icons"></a>Las barras de herramientas y sus iconos

Uno de los usos principales de los mapas de bits específicos de la plataforma es la barra de herramientas de Xamarin. Forms, que se construye agregando [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos a la colección de [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) definida por `Page`. `ToobarItem` deriva de [`MenuItem`](xref:Xamarin.Forms.MenuItem) del que hereda algunas propiedades.

Las propiedades de `ToolbarItem` más importantes son:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) para el texto que puede aparecer en función de la plataforma y `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de tipo `FileImageSource` para la imagen que puede aparecer en función de la plataforma y `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) de tipo [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder), una enumeración con tres miembros, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default), [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)y [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

El número de elementos `Primary` debe estar limitado a tres o cuatro. Debe incluir un valor de `Text` para todos los elementos. Para la mayoría de las plataformas, solo los elementos `Primary` requieren un `Icon` pero Windows 8.1 requiere un `Icon` para todos los elementos. Los iconos deben tener 32 unidades independientes del dispositivo cuadradas. El tipo de `FileImageSource` indica que son específicos de la plataforma.

El `ToolbarItem` activa un evento de [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) al puntear, de forma muy parecida a una `Button`. `ToolbarItem` también admite las propiedades [`Command`](xref:Xamarin.Forms.MenuItem.Command) y [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) que se usan a menudo en conexión con MVVM. (Consulte el [capítulo 18, MVVM](chapter18.md)).

Tanto iOS como Android requieren que una página que muestre una barra de herramientas sea un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) o una página a la que se haya navegado mediante un `NavigationPage`. El programa [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) establece la propiedad `MainPage` de su clase `App` en el [constructor`NavigationPage`](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) con un argumento `ContentPage` y muestra el controlador de eventos y la construcción de una barra de herramientas.

### <a name="button-images"></a>Imágenes de botón

También puede usar mapas de bits específicos de la plataforma para establecer la propiedad [`Image`](xref:Xamarin.Forms.Button.Image) de `Button` en un mapa de bits de unidades independientes del dispositivo 32, tal como se muestra en el ejemplo [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> Se ha mejorado el uso de imágenes en los botones. Vea [usar mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Vínculos relacionados

- [Capítulo 13 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Ejemplos del capítulo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Working with Images (Trabajo con imágenes)](~/xamarin-forms/user-interface/images.md)
- [Usar mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)
