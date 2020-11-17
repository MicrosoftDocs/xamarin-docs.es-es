---
title: Resumen del capítulo 13. Mapas de bits
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 13. Mapas de bits'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 011ef7c73e756c938589124676fa3d9c14a3b556
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374852"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Resumen del capítulo 13. Mapas de bits

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Este libro se publicó en la primavera de 2016 y no se ha actualizado desde entonces. Gran parte del libro sigue siendo útil, pero algunos de los materiales están anticuados y algunos temas ya no son completamente correctos o completos.

El elemento [`Image`](xref:Xamarin.Forms.Image) de Xamarin.Forms muestra un mapa de bits. Todas las plataformas de Xamarin.Forms admiten los formatos de archivo JPEG, PNG, GIF y BMP.

Los mapas de bits de Xamarin.Forms provienen de cuatro lugares:

- A través de la web como se especifica en una dirección URL
- Incrustados como un recurso en la biblioteca compartida
- Incrustados como un recurso en proyectos de la aplicación de plataforma
- Desde cualquier parte a la que se pueda hacer referencia con un objeto `Stream` de .NET, como `MemoryStream`.

Los recursos de mapa de bits de la biblioteca compartida son independientes de la plataforma, mientras que los recursos de mapa de bits de los proyectos de plataforma son específicos de la plataforma.

> [!NOTE]
> El texto del libro hace referencia a las bibliotecas de clases portables, que se han reemplazado por las bibliotecas .NET Standard. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas .NET Standard.

El mapa de bits se especifica mediante el establecimiento de la propiedad [`Source`](xref:Xamarin.Forms.Image.Source) de `Image` en un objeto de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), una clase abstracta con tres derivados:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) para acceder a un mapa de bits a través de la web en función de un objeto `Uri` establecido en su propiedad [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri).
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) para acceder a un mapa de bits almacenado en un proyecto de aplicación de plataforma en función de una ruta de acceso a archivos y carpetas establecida en su propiedad [`File`](xref:Xamarin.Forms.FileImageSource.File).
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) para cargar un mapa de bits mediante un objeto `Stream` de .NET especificado por medio de la devolución de un elemento `Stream` desde un elemento `Func` establecido en su propiedad [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream).

Como alternativa (y de forma más común), se pueden usar los siguientes métodos estáticos de la clase `ImageSource`, todos los cuales devuelven objetos `ImageSource`:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) para acceder a un mapa de bits a través de la web en función de un objeto `Uri`.
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) para acceder a un mapa de bits almacenado como un recurso incrustado en la PCL de la aplicación; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) o [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) para acceder a un mapa de bits en otro ensamblado de origen.
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) para acceder a un mapa de bits desde un proyecto de aplicación de la plataforma.
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) para cargar un mapa de bits en función de un objeto `Stream`.

No hay ninguna clase equivalente de los métodos `Image.FromResource`. La clase `UriImageSource` es útil si necesita controlar el almacenamiento en caché. La clase `FileImageSource` es útil en XAML. `StreamImageSource` es útil para la carga asincrónica de objetos `Stream`, mientras que `ImageSource.FromStream` es sincrónico.

## <a name="platform-independent-bitmaps"></a>Mapas de bits independientes de la plataforma

El proyecto [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) carga un mapa de bits a través de la web mediante `ImageSource.FromUri`. El elemento `Image` se establece en la propiedad `Content` de `ContentPage`, por lo que se limita al tamaño de la página. Con independencia del tamaño del mapa de bits, un elemento `Image` restringido se ajusta al tamaño de su contenedor y el mapa de bits se muestra en su tamaño máximo dentro del elemento `Image` mientras se mantiene la relación de aspecto del mapa de bits. Las áreas de `Image` más allá del mapa de bits se pueden colorear con [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor).

El ejemplo [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) es similar, pero simplemente establece la propiedad `Source` en la dirección URL. La conversión se controla mediante la clase [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter).

### <a name="fit-and-fill"></a>Ajuste y relleno

Puede controlar cómo se ajusta el mapa de bits mediante el establecimiento de la propiedad [`Aspect`](xref:Xamarin.Forms.Image.Aspect) de `Image` en uno de los siguientes miembros de la enumeración [`Aspect`](xref:Xamarin.Forms.Aspect):

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): respeta la relación de aspecto (valor predeterminado).
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): rellena el área, no respeta la relación de aspecto.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): rellena el área pero respeta la relación de aspecto, al recortar parte del mapa de bits.

### <a name="embedded-resources"></a>Recursos incrustados

Puede agregar un archivo de mapa de bits a una PCL o a una de sus carpetas. Asígnele una **acción de compilación** de **EmbeddedResource**. En el ejemplo [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) se muestra cómo utilizar `ImageSource.FromResource` para cargar el archivo. El nombre del recurso que se pasa al método consta del nombre del ensamblado, seguido de un punto, seguido del nombre de carpeta opcional y un punto, seguido del nombre de archivo.

El programa establece las propiedades `VerticalOptions` y `HorizontalOptions` de `Image` en `LayoutOptions.Center`, lo que hace que el elemento `Image` quede sin restricciones. `Image` y el mapa de bits tienen el mismo tamaño:

- En iOS y Android, `Image` tiene el tamaño de píxel del mapa de bits. Hay una asignación uno a uno entre los píxeles del mapa de bits y los píxeles de la pantalla.
- En la Plataforma universal de Windows, `Image` tiene el tamaño de píxel del mapa de bits en unidades independientes del dispositivo. En la mayoría de los dispositivos, cada píxel de mapa de bits ocupa varios píxeles de pantalla.

En el ejemplo [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) se coloca un elemento `Image` en un elemento `StackLayout` vertical en XAML. Una extensión de marcado con el nombre [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) ayuda a hacer referencia al recurso incrustado en XAML. Esta clase solo carga los recursos del ensamblado en el que se encuentra, por lo que no se puede colocar en una biblioteca.

### <a name="more-on-sizing"></a>Más sobre el ajuste de tamaño

A menudo es conveniente cambiar el tamaño de los mapas de bits de forma coherente en todas las plataformas.
Experimentando con **StackedBitmap**, puede establecer un elemento `WidthRequest` en el elemento `Image` de un elemento `StackLayout` vertical para que el tamaño sea coherente entre las plataformas, pero solo puede reducir el tamaño con esta técnica.

También puede establecer `HeightRequest` para que los tamaños de imagen sean coherentes en las plataformas, pero el ancho restringido del mapa de bits limitará la versatilidad de esta técnica. En el caso de una imagen de un elemento `StackLayout` vertical, se debe evitar `HeightRequest`.

El mejor enfoque es comenzar con un mapa de bits más ancho que el ancho del teléfono en unidades independientes del dispositivo y establecer `WidthRequest` en un ancho deseado en unidades independientes del dispositivo. Esto se muestra en el ejemplo [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize).

En [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) se muestra el capítulo 7 de las *Aventuras de Alicia en el País de las Maravillas* de Lewis Carroll con las ilustraciones originales de John Tenniel:

[![Captura de pantalla triple de la fiesta de té loco](images/ch13fg16-small.png "Texto del libro de la fiesta del té de los sombrereros locos")](images/ch13fg16-large.png#lightbox "Texto del libro de la fiesta del té de los sombrereros locos")

### <a name="browsing-and-waiting"></a>Examinar y esperar

El ejemplo [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permite al usuario examinar las imágenes de existencias almacenadas en el sitio web de Xamarin. Se usa la clase [`WebRequest`](xref:System.Net.WebRequest) de .NET para descargar un archivo JSON con la lista de mapas de bits.

> [!NOTE]
> Los programas de Xamarin.Forms deben usar [`HttpClient`](xref:System.Net.Http.HttpClient) en lugar de [`WebRequest`](xref:System.Net.WebRequest) para acceder a los archivos a través de Internet.

El programa usa un elemento [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) para indicar que algo pasa. A medida que se carga el mapa de bits, la propiedad [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) de solo lectura `Image` es `true`. La propiedad `IsLoading` está respaldada por una propiedad enlazable, por lo que se desencadena un evento `PropertyChanged` cuando esa propiedad cambia. El programa adjunta un controlador a este evento y usa la configuración actual de `IsLoaded` para establecer la propiedad [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) de `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Mapas de bits de streaming

El método `ImageSource.FromStream` crea un elemento `ImageSource` en función de `Stream` de .NET. Se debe pasar al método un objeto `Func` que devuelva un objeto `Stream`.

### <a name="accessing-the-streams"></a>Acceso a las secuencias

En el ejemplo [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) se muestra cómo usar el método `ImaageSource.FromStream` para cargar un mapa de bits almacenado como un recurso incrustado, y para cargar un mapa de bits en la web.

### <a name="generating-bitmaps-at-run-time"></a>Generación de mapas de bits en tiempo de ejecución

Todas las plataformas Xamarin.Forms admiten el formato de archivo BMP sin comprimir, que es fácil de construir en el código y luego almacenar en un elemento `MemoryStream`. Esta técnica permite la creación algorítmica de mapas de bits en tiempo de ejecución, tal y como se implementa en la clase [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) de la biblioteca de **Xamarin.FormsBook.Toolkit**.

El ejemplo [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) de "Hágalo usted mismo" muestra el uso de `BmpMaker` para crear un mapa de bits con una imagen de degradado.

## <a name="platform-specific-bitmaps"></a>Mapas de bits específicos de la plataforma

Todas las plataformas Xamarin.Forms permiten almacenar mapas de bits en los ensamblados de aplicación de la plataforma. Cuando una aplicación Xamarin.Forms los recupera, estos mapas de bits de plataforma son de tipo [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Úselos para:

- la propiedad [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- la propiedad [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- la propiedad [`Image`](xref:Xamarin.Forms.Button) de `Button`

Los ensamblados de plataforma ya contienen mapas de bits para iconos y pantallas de presentación:

- En el proyecto de iOS, en la carpeta **Resources**.
- En el proyecto de Android, en las subcarpetas de la carpeta **Resources**.
- En los proyectos de Windows, en la carpeta **Assets** (aunque las plataformas de Windows no restringen los mapas de bits a esa carpeta).

En el ejemplo [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) se usa código para mostrar un icono de los proyectos de aplicación de la plataforma.

### <a name="bitmap-resolutions"></a>Resoluciones del mapa de bits

Todas las plataformas permiten almacenar varias versiones de imágenes de mapa de bits para diferentes resoluciones de dispositivos. En tiempo de ejecución, la versión correcta se carga en función de la resolución de la pantalla del dispositivo.

En iOS, estos mapas de bits se diferencian por un sufijo en el nombre de archivo:

- Ningún sufijo para dispositivos de 160 ppp (1 píxel para la unidad independiente del dispositivo)
- Sufijo "@2x" para dispositivos de 320 ppp (2 píxeles para la DIU)
- Sufijo "@3x" para dispositivos de 480 ppp (3 píxeles para la DIU)

Un mapa de bits que se vaya a mostrar como una pulgada cuadrada existirá en tres versiones:

- MyImage.jpg en un cuadrado de 160 píxeles
- MyImage@2x.jpg en un cuadrado de 320 píxeles
- MyImage@3x.jpg en un cuadrado de 480 píxeles

El programa haría referencia a este mapa de bits como MyImage.jpg, pero la versión correcta se recupera en tiempo de ejecución en función de la resolución de la pantalla. Si no está restringido, el mapa de bits siempre se representará en 160 unidades independientes del dispositivo.

En el caso de Android, los mapas de bits se almacenan en varias subcarpetas de la carpeta **Resources**:

- drawable-ldpi (ppp bajo) para dispositivos de 120 ppp (0,75 píxeles para la DIU)
- drawable-mdpi (medio) para dispositivos de 160 ppp (1 píxel para la DIU)
- drawable-mdpi (alto) para dispositivos de 240 ppp (1,5 píxeles para la DIU)
- drawable-mdpi (muy alto) para dispositivos de 320 ppp (2 píxeles para la DIU)
- drawable-mdpi (muy muy alto) para dispositivos de 480 ppp (3 píxeles para la DIU)
- drawable-mdpi (tres muy alto) para dispositivos de 640 ppp (4 píxeles para la DIU)

En el caso de un mapa de bits que se vaya a representar en una pulgada cuadrada, las distintas versiones del mapa de bits tendrán el mismo nombre pero con un tamaño diferente, y se almacenarán en estas carpetas:

- drawable-ldpi/MyImage.jpg en un cuadrado de 120 píxeles
- drawable-mdpi/MyImage.jpg en un cuadrado de 160 píxeles
- drawable-hdpi/MyImage.jpg en un cuadrado de 240 píxeles
- drawable-xhdpi/MyImage.jpg en un cuadrado de 320 píxeles
- drawable-xxhdpi/MyImage.jpg en un cuadrado de 480 píxeles
- drawable-xxxhdpi/MyImage.jpg en un cuadrado de 640 píxeles

El mapa de bits siempre se representará en 160 unidades independientes del dispositivo. (La plantilla de la solución Xamarin.Forms estándar solo incluye las carpetas hdpi, xhdpi y xxhdpi).

El proyecto UWP admite un esquema de nomenclatura de mapa de bits que consta de un factor de escala en píxeles por unidad independiente del dispositivo como un porcentaje, por ejemplo:

- MyImage.scale-200.jpg en un cuadrado de 320 píxeles

Solo algunos porcentajes son válidos. Los programas de ejemplo de este libro incluyen solo imágenes con sufijos **scale-200**, pero las plantillas de soluciones actuales de Xamarin.Forms incluyen **scale-100**, **scale-125**, **scale-150** y **scale-400**.

Al agregar mapas de bits a los proyectos de la plataforma, la **acción de compilación** debe ser:

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **Contenido**

En el ejemplo [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) se crean dos objetos tipo botón que constan de elementos `Image` con `TapGestureRecognizer` instalado. El objetivo es que los objetos sean de un pulgada cuadrada. La propiedad `Source` de `Image` se establece mediante objetos `OnPlatform` y `On` para hacer referencia a nombres de archivo potencialmente diferentes en las plataformas. Las imágenes de mapa de bits incluyen números que indican su tamaño en píxeles, por lo que puede ver qué tamaño de mapa de bits se recupera y se representa.

### <a name="toolbars-and-their-icons"></a>Barras de herramientas y sus iconos

Uno de los usos principales de los mapas de bits específicos de la plataforma es la barra de herramientas de Xamarin.Forms, que se construye mediante la adición de objetos [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) a la colección [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) definida por `Page`. `ToobarItem` se deriva de [`MenuItem`](xref:Xamarin.Forms.MenuItem) del que hereda algunas propiedades.

Las dos propiedades `ToolbarItem` más importantes son:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) para el texto que puede aparecer en función de la plataforma y `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de tipo `FileImageSource` para la imagen que puede aparecer en función de la plataforma y `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) de tipo [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder), una enumeración con tres miembros, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default), [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary) y [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

El número de elementos `Primary` debe estar limitado a tres o cuatro. Debe incluir un valor `Text` para todos los elementos. En la mayoría de las plataformas, solo los elementos `Primary` requieren un objeto `Icon`, pero Windows 8.1 requiere un objeto `Icon` para todos. Los iconos deben tener 32 unidades cuadradas independientes del dispositivo. El tipo `FileImageSource` indica que son específicos de la plataforma.

`ToolbarItem` activa un evento [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) cuando se pulsa, de forma muy parecida a una `Button`. `ToolbarItem` también admite propiedades [`Command`](xref:Xamarin.Forms.MenuItem.Command) y [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) que se usan a menudo en conexión con MVVM. (Consulte el [Capítulo 18, MVVM](chapter18.md)).

Tanto iOS como Android requieren que una página que muestre una barra de herramientas sea [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) o una página a la que se haya ido mediante `NavigationPage`. El programa [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) establece la propiedad `MainPage` de su clase `App` en el [`NavigationPage` constructor](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) con un argumento `ContentPage`, y muestra el controlador de eventos y la construcción de una barra de herramientas.

### <a name="button-images"></a>Imágenes de botón

También puede usar mapas de bits específicos de la plataforma para establecer la propiedad [`Image`](xref:Xamarin.Forms.Button.Image) de `Button` en un mapa de bits de 32 unidades cuadradas independientes del dispositivo, como se muestra en el ejemplo [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage).

> [!NOTE]
> El uso de imágenes en botones se ha mejorado. Consulte [Uso de mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 13 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Ejemplos del capítulo 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Working with Images (Trabajo con imágenes)](~/xamarin-forms/user-interface/images.md)
- [Uso de mapas de bits con botones](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
