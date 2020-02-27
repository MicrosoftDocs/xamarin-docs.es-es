---
title: Vistas de Xamarin.Forms
description: Las vistas de Xamarin.Forms son los bloques de creación de interfaces de usuario móviles multiplataforma. En este artículo se enumera las vistas que se incluyen en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/14/2020
ms.openlocfilehash: 1e8b6f5e1ea090abc8ebd6084095bf6b34663a42
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635876"
---
# <a name="xamarinforms-views"></a>Vistas de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Las vistas de Xamarin. Forms son los bloques de creación de interfaces de usuario móviles multiplataforma._

Las vistas son objetos de la interfaz de usuario, como etiquetas, botones y controles deslizantes que normalmente se conocen como *controles* o *widgets* en otros entornos de programación gráficos. Todas las vistas compatibles con Xamarin. Forms derivan de la clase [`View`](xref:Xamarin.Forms.View) . Pueden dividirse en varias categorías:

## <a name="views-for-presentation"></a>Vistas para presentación

### <a name="label"></a>Etiqueta

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label) muestra cadenas de texto de una sola línea o bloques de texto de varias líneas, con un formato constante o variable. Establezca la propiedad [`Text`](xref:Xamarin.Forms.Label.Text) en una cadena para el formato constante o establezca la propiedad [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) en un objeto [`FormattedString`](xref:Xamarin.Forms.FormattedString) para el formato de variables.<br /><br />[Documentación de API](xref:Xamarin.Forms.Label) / [Guía](~/xamarin-forms/user-interface/text/label.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Ejemplo de etiqueta](views-images/Label.png "Ejemplo de etiqueta")](views-images/Label-Large.png#lightbox "Ejemplo de etiqueta")<br /> código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) |
|     |     |

### <a name="image"></a>Imagen

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image) muestra un mapa de bits. Los mapas de bits se pueden descargar a través de Web, incrustados como recursos en los proyectos comunes de proyecto o plataforma, o bien crearse mediante un objeto de `Stream` .NET.<br /><br />[Documentación de API](xref:Xamarin.Forms.Image) / [Guía](~/xamarin-forms/user-interface/images.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) | [![Ejemplo de imagen](views-images/Image.png "Ejemplo de imagen")](views-images/Image-Large.png#lightbox "Ejemplo de imagen")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView) muestra un rectángulo sólido coloreado por la propiedad [`Color`](xref:Xamarin.Forms.BoxView.Color) . `BoxView` tiene una solicitud de tamaño predeterminado de 40 x 40. Para otros tamaños, asigne las propiedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) y [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) .<br /><br />[Documentación de API](xref:Xamarin.Forms.BoxView) / [Guía](~/xamarin-forms/user-interface/boxview.md) / [ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)y [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Ejemplo de BoxView](views-images/BoxView.png "Ejemplo de BoxView")](views-images/BoxView-Large.png#lightbox "Ejemplo de BoxView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView) muestra páginas web o contenido HTML, en función de si la propiedad [`Source`](xref:Xamarin.Forms.WebView.Source) está establecida en un [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) o un objeto [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) .<br /><br />[Documentación de API](xref:Xamarin.Forms.WebView) / [Guía](~/xamarin-forms/user-interface/webview.md) / [ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Ejemplo de WebView](views-images/WebView.png "Ejemplo de WebView")](views-images/WebView-Large.png#lightbox "Ejemplo de WebView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView) muestra gráficos OpenGL en los proyectos de iOS y Android. No hay ninguna compatibilidad para la plataforma Universal de Windows. Los proyectos de iOS y Android requieren una referencia al ensamblado **OpenTK-1,0** o al ensamblado de la versión 1.0.0.0 de **OpenTK** . `OpenGLView` es más fácil de usar en un proyecto compartido; Si se utiliza en una biblioteca de .NET Standard, también se requerirá un servicio de dependencia (como se muestra en el código de ejemplo).<br /><br />Esta es la única instalación de gráficos que está integrada en Xamarin. Forms, pero una aplicación de Xamarin. Forms también puede representar gráficos mediante [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md), o [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentación de la API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Ejemplo de OpenGLView](views-images/OpenGLView.png "Ejemplo de OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Ejemplo de OpenGLView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) |
|     |     |

### <a name="map"></a>Map

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map) muestra un mapa. El paquete de NuGet **Xamarin. Forms. Maps** debe estar instalado. Android y plataforma Universal de Windows requieren una clave de autorización de mapa.<br /><br />[Documentación de API](xref:Xamarin.Forms.Maps.Map) / [Guía](~/xamarin-forms/user-interface/map/index.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) | [![Ejemplo de mapa](views-images/Map.png "Ejemplo de mapa")](views-images/Map-Large.png#lightbox "Ejemplo de mapa")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement) reproduce vídeo o audio. Los medios se pueden reproducir desde una dirección URL o desde un archivo local, en función de si la propiedad [`Source`](xref:Xamarin.Forms.MediaElement.Source) está establecida en un [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) o un [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource).<br /><br />[Documentación de API](xref:Xamarin.Forms.MediaElement) / [Guía](~/xamarin-forms/user-interface/mediaelement.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) | [![Ejemplo de MediaElement](views-images/MediaElement.png "Ejemplo de MediaElement")](views-images/MediaElement-Large.png#lightbox "Ejemplo de MediaElement")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs) |
|     |     |

## <a name="views-that-initiate-commands"></a>Vistas que inician comandos

### <a name="button"></a>Botón

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button) es un objeto rectangular que muestra texto y que activa un evento de [`Clicked`](xref:Xamarin.Forms.Button.Clicked) cuando se presiona.<br /><br />[Documentación de API](xref:Xamarin.Forms.Button) / [Guía](~/xamarin-forms/user-interface/button.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) | [![Ejemplo de botón](views-images/Button.png "Ejemplo de botón")](views-images/Button-Large.png#lightbox "Ejemplo de botón")<br /> código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton` es un objeto rectangular que muestra una imagen y que activa un evento de `Clicked` cuando se presiona.<br /><br /> [Guía](~/xamarin-forms/user-interface/imagebutton.md) de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) /  | [![Ejemplo de ImageButton](views-images/ImageButton.png "Ejemplo de ImageButton")](views-images/ImageButton-Large.png#lightbox "Ejemplo de ImageButton")<br /> código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView` es un control contenedor que proporciona funcionalidad de extracción a actualización para el contenido desplazable. Los `ICommand` definidos por la propiedad `Command` se ejecutan cuando se desencadena una actualización, y la propiedad `IsRefreshing` indica el estado actual del control.<br /><br /> [Guía](~/xamarin-forms/user-interface/refreshview.md) de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) /  | [![Ejemplo de RefreshView](views-images/RefreshView.png "Ejemplo de RefreshView")](views-images/RefreshView-Large.png#lightbox "Ejemplo de RefreshView")<br /> código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) |
|     |     |

### <a name="searchbar"></a>Barra de búsqueda

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar) muestra un área para que el usuario escriba una cadena de texto y un botón (o una tecla del teclado) que indique a la aplicación que realice una búsqueda. La propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) proporciona acceso al texto y el evento [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) indica que se ha presionado el botón.<br /><br />[Documentación de API](xref:Xamarin.Forms.SearchBar) / [Guía](~/xamarin-forms/user-interface/searchbar.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) | [![Ejemplo de barra](views-images/SearchBar.png "Ejemplo de barra")](views-images/SearchBar-Large.png#lightbox "Ejemplo de barra")<br /> código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView` es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente. Cada elemento de menú se representa mediante una `SwipeItem`, que tiene una propiedad `Command` que ejecuta un `ICommand` cuando se puntea el elemento.<br /><br /> [Guía](~/xamarin-forms/user-interface/swipeview.md) de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) /  | [![Ejemplo de SwipeView](views-images/SwipeView.png "Ejemplo de SwipeView")](views-images/SwipeView-Large.png#lightbox "Ejemplo de SwipeView")<br /> código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Vistas para establecer valores

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox` permite al usuario seleccionar un valor booleano mediante un tipo de botón que puede estar activado o vacío. La propiedad `IsChecked` es el estado del `CheckBox`y el evento `CheckedChanged` se desencadena cuando cambia el estado.<br /><br />Documentación/ [Guía](~/xamarin-forms/user-interface/checkbox.md) de la API / [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Ejemplo de CheckBox](views-images/CheckBox.png "Ejemplo de CheckBox")](views-images/CheckBox-Large.png#lightbox "Ejemplo de CheckBox")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs) |
|     |     |

### <a name="slider"></a>Control deslizante

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider) permite al usuario seleccionar un valor de `double` de un intervalo continuo especificado con las propiedades [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) y [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) .<br /><br />[Documentación de API](xref:Xamarin.Forms.Slider) / [Guía](~/xamarin-forms/user-interface/slider.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) | [![Ejemplo de control deslizante](views-images/Slider.png "Ejemplo de control deslizante")](views-images/Slider-Large.png#lightbox "Ejemplo de control deslizante")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) |
|     |     |

### <a name="stepper"></a>Control de incremento

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper) permite al usuario seleccionar un valor de `double` de un intervalo de valores incrementales especificado con las propiedades [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum), [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)y [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[Documentación de API](xref:Xamarin.Forms.Stepper)  / [Guía](~/xamarin-forms/user-interface/stepper.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) | [![Ejemplo de stepper](views-images/Stepper.png "Ejemplo de stepper")](views-images/Stepper-Large.png#lightbox "Ejemplo de stepper")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) |
|     |     |

### <a name="switch"></a>Switch

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch) adopta la forma de un modificador ON/OFF para permitir al usuario seleccionar un valor booleano. La propiedad [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) es el estado del modificador y el evento [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) se desencadena cuando cambia el estado.<br /><br />[Documentación de API](xref:Xamarin.Forms.Switch) / [Guía](~/xamarin-forms/user-interface/switch.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) | [![Ejemplo de conmutador](views-images/Switch.png "Ejemplo de conmutador")](views-images/Switch-Large.png#lightbox "Ejemplo de conmutador")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker) permite al usuario seleccionar una fecha con el selector de fecha de la plataforma. Establezca un intervalo de fechas permitidas con las propiedades [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) y [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) . La propiedad [`Date`](xref:Xamarin.Forms.DatePicker.Date) es la fecha seleccionada y el evento [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) se desencadena cuando cambia esa propiedad.<br /><br />[Documentación de API](xref:Xamarin.Forms.DatePicker) / [Guía](~/xamarin-forms/user-interface/datepicker.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) | [![Ejemplo de DatePicker](views-images/DatePicker.png "Ejemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "Ejemplo de DatePicker")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker) permite al usuario seleccionar una hora con el selector de tiempo de plataforma. La propiedad [`Time`](xref:Xamarin.Forms.TimePicker.Time) es la hora seleccionada. Una aplicación puede supervisar los cambios en la propiedad `Time` mediante la instalación de un controlador para el evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .<br /><br />[Documentación de API](xref:Xamarin.Forms.TimePicker) / [Guía](~/xamarin-forms/user-interface/timepicker.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) | [![Ejemplo de TimePicker](views-images/TimePicker.png "Ejemplo de TimePicker")](views-images/TimePicker-Large.png#lightbox "Ejemplo de TimePicker")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) |
|     |     |

## <a name="views-for-editing-text"></a>Vistas para editar texto

Estas dos clases derivan de la clase [`InputView`](xref:Xamarin.Forms.InputView) , que define la propiedad [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) .

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry) permite al usuario escribir y editar una sola línea de texto. El texto está disponible como la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) , y los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Entry.Completed) se activan cuando cambia el texto o el usuario indica la finalización punteando en la tecla entrar.<br /><br />Use un [`Editor`](#editor) para escribir y editar varias líneas de texto.<br /><br />[Documentación de API](xref:Xamarin.Forms.Entry) / [Guía](~/xamarin-forms/user-interface/text/entry.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Ejemplo de entrada](views-images/Entry.png "Ejemplo de entrada")](views-images/Entry-Large.png#lightbox "Ejemplo de entrada")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor) permite al usuario escribir y editar varias líneas de texto. El texto está disponible como la propiedad [`Text`](xref:Xamarin.Forms.InputView.Text) , y los eventos [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) y [`Completed`](xref:Xamarin.Forms.Editor.Completed) se activan cuando cambia el texto o el usuario envía una señal de finalización.<br /><br />Use una vista [`Entry`](#entry) para escribir y editar una sola línea de texto.<br /><br />[Documentación de API](xref:Xamarin.Forms.Editor) / [Guía](~/xamarin-forms/user-interface/text/editor.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) | [![Ejemplo de entrada](views-images/Editor.png "Ejemplo de editor")](views-images/Editor-Large.png#lightbox "Ejemplo de editor")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) |
|     |     |

## <a name="views-to-indicate-activity"></a>Vistas para indicar actividad

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) usa una animación para mostrar que la aplicación está ocupada en una actividad prolongada sin ofrecer ninguna indicación de progreso. La propiedad [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) controla la animación.<br /><br />Si se conoce el progreso de la actividad, use un [`ProgressBar`](#progressbar) en su lugar.<br /><br />[Documentación de API](xref:Xamarin.Forms.ActivityIndicator) / [Guía](~/xamarin-forms/user-interface/activityindicator.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) | [![Ejemplo de ActivityIndicator](views-images/ActivityIndicator.png "Ejemplo de ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Ejemplo de ActivityIndicator")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) |
|     |     |

### <a name="progressbar"></a>Barra de progreso

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) usa una animación para mostrar que la aplicación progresa a través de una actividad prolongada. Establezca la propiedad [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) en valores entre 0 y 1 para indicar el progreso.<br /><br />Si no se conoce el progreso de la actividad, use un [`ActivityIndicator`](#activityindicator) en su lugar.<br /><br />[Documentación de API](xref:Xamarin.Forms.ProgressBar) / [Guía](~/xamarin-forms/user-interface/progressbar.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) | [![Ejemplo de ProgressBar](views-images/ProgressBar.png "Ejemplo de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Ejemplo de ProgressBar")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Vistas que muestran colecciones

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| en [`CarouselView`](xref:Xamarin.Forms.CarouselView) se muestra una lista desplazable de elementos de datos. Establezca la propiedad `ItemsSource` en una colección de objetos y establezca la propiedad `ItemTemplate` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que describa cómo se va a dar formato a los elementos. El evento `CurrentItemChanged` indica que el elemento mostrado actualmente ha cambiado, que está disponible como la propiedad `CurrentItem`.<br /><br />[Guía](~/xamarin-forms/user-interface/carouselview/index.md) de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) /  | [![Ejemplo de CarouselView](views-images/CarouselView.png "Ejemplo de CarouselView")](views-images/CarouselView-Large.png#lightbox "Ejemplo de CarouselView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| en [`CollectionView`](xref:Xamarin.Forms.CollectionView) se muestra una lista desplazable de elementos de datos seleccionables, con distintas especificaciones de diseño. Pretende proporcionar una alternativa más flexible y eficaz a [`ListView`](xref:Xamarin.Forms.ListView). Establezca la propiedad `ItemsSource` en una colección de objetos y establezca la propiedad `ItemTemplate` en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que describa cómo se va a dar formato a los elementos. El evento `SelectionChanged` indica que se ha realizado una selección, que está disponible como la propiedad `SelectedItem`.<br /><br />[Guía](~/xamarin-forms/user-interface/collectionview/index.md) de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) /  | [![Ejemplo de CollectionView](views-images/CollectionView.png "Ejemplo de CollectionView")](views-images/CollectionView-Large.png#lightbox "Ejemplo de CollectionView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView` muestra indicadores que representan el número de elementos de un `CarouselView`. Establezca la propiedad `IndicatorView.ItemsSourceBy` adjunta en el objeto `CarouselView` para mostrar los indicadores. <br /><br />[Guía](~/xamarin-forms/user-interface/indicatorview.md) de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) /  | [![Ejemplo de IndicatorView](views-images/IndicatorView.png "Ejemplo de IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Ejemplo de IndicatorView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView) deriva de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) y muestra una lista desplazable de elementos de datos seleccionables. Establezca la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) en una colección de objetos y establezca la propiedad [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) en un objeto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que describa cómo se va a dar formato a los elementos. El evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) indica que se ha realizado una selección, que está disponible como la propiedad [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) .<br /><br />[Documentación de API](xref:Xamarin.Forms.ListView) / [Guía](~/xamarin-forms/user-interface/listview/index.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) | [![Ejemplo de ListView](views-images/ListView.png "Ejemplo de ListView")](views-images/ListView-Large.png#lightbox "Ejemplo de ListView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) |
|     |     |

### <a name="picker"></a>Selector

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker) muestra un elemento seleccionado en una lista de cadenas de texto y permite seleccionar ese elemento cuando se puntea en la vista. Establezca la propiedad [`Items`](xref:Xamarin.Forms.Picker.Items) en una lista de cadenas o la propiedad [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) en una colección de objetos. El evento [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) se desencadena cuando se selecciona un elemento.<br /><br />El `Picker` muestra la lista de elementos solo cuando está seleccionado. Use un [`ListView`](#listview) o [`TableView`](#tableview) para una lista desplazable que permanezca en la página.<br /><br />[Documentación de API](xref:Xamarin.Forms.Picker) / [Guía](~/xamarin-forms/user-interface/picker/index.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) | [![Ejemplo del selector](views-images/Picker.png "Ejemplo del selector")](views-images/Picker-Large.png#lightbox "Ejemplo del selector")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| en [`TableView`](xref:Xamarin.Forms.TableView) se muestra una lista de filas de tipo [`Cell`](xref:Xamarin.Forms.Cell) con encabezados y subencabezados opcionales. Establezca la propiedad [`Root`](xref:Xamarin.Forms.TableView.Root) en un objeto de tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot)y agregue objetos [`TableSection`](xref:Xamarin.Forms.TableSection) a ese `TableRoot`. Cada `TableSection` es una colección de objetos `Cell`.<br /><br />[Documentación de API](xref:Xamarin.Forms.TableView) / [Guía](~/xamarin-forms/user-interface/tableview.md) de / de [ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) | [![Ejemplo de TableView](views-images/TableView.png "Ejemplo de TableView")](views-images/TableView-Large.png#lightbox "Ejemplo de TableView")<br />código para esta página / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Samples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms) (Ejemplos de Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
