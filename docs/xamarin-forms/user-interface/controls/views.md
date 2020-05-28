---
title: Xamarin.FormsVistas
description: Xamarin.Formslas vistas son los bloques de creación de interfaces de usuario móviles multiplataforma. En este artículo se enumeran las vistas que se incluyen en Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c15626e405645d28a785c32d276860f9751ea25
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132384"
---
# <a name="xamarinforms-views"></a>Xamarin.FormsVistas

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Las vistas de Xamarin. Forms son los bloques de creación de interfaces de usuario móviles multiplataforma._

Las vistas son objetos de la interfaz de usuario, como etiquetas, botones y controles deslizantes que normalmente se conocen como *controles* o *widgets* en otros entornos de programación gráficos. Las vistas admitidas por Xamarin.Forms todas derivan de la [`View`](xref:Xamarin.Forms.View) clase. Se pueden dividir en varias categorías:

## <a name="views-for-presentation"></a>Vistas para presentación

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)muestra un rectángulo sólido coloreado por la [`Color`](xref:Xamarin.Forms.BoxView.Color) propiedad. `BoxView`tiene una solicitud de tamaño predeterminado de 40 x 40. Para otros tamaños, asigne las [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) propiedades y.<br /><br />[Documentación](xref:Xamarin.Forms.BoxView)  /  de API [Guía](~/xamarin-forms/user-interface/boxview.md)  /  de [Ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)y [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Ejemplo de BoxView](views-images/BoxView.png "Ejemplo de BoxView")](views-images/BoxView-Large.png#lightbox "Ejemplo de BoxView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="expander"></a>Expander

|     |     |
| --- | --- |
| `Expander`proporciona un contenedor expansible para hospedar cualquier contenido y se compone de un encabezado y contenido. Establezca la `Header` propiedad en un [`View`](xref:Xamarin.Forms.View) que se mostrará como el encabezado y la `Content` propiedad en un que se [`View`](xref:Xamarin.Forms.View) mostrará cuando el encabezado se expanda mediante una derivación.<br /><br />[Guía](~/xamarin-forms/user-interface/expander.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) de | [![Ejemplo de expansor](views-images/Expander.png "Ejemplo de expansor")](views-images/Expander-Large.png#lightbox "Ejemplo de expansor")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>Etiqueta

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)muestra cadenas de texto de una sola línea o bloques de texto de varias líneas, con un formato constante o variable. Establezca la [`Text`](xref:Xamarin.Forms.Label.Text) propiedad en una cadena para el formato constante o establezca la [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propiedad en un [`FormattedString`](xref:Xamarin.Forms.FormattedString) objeto para el formato de variable.<br /><br />[Documentación](xref:Xamarin.Forms.Label)  /  de API [Guía](~/xamarin-forms/user-interface/text/label.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de | [![Ejemplo de etiqueta](views-images/Label.png "Ejemplo de etiqueta")](views-images/Label-Large.png#lightbox "Ejemplo de etiqueta")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Imagen

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)muestra un mapa de bits. Los mapas de bits se pueden descargar a través de Web, incrustados como recursos en los proyectos comunes de proyecto o plataforma, o bien crearse mediante un `Stream` objeto .net.<br /><br />[Documentación](xref:Xamarin.Forms.Image)  /  de API [Guía](~/xamarin-forms/user-interface/images.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) de | [![Ejemplo de imagen](views-images/Image.png "Ejemplo de imagen")](views-images/Image-Large.png#lightbox "Ejemplo de imagen")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>Mapa

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)muestra un mapa. El ** Xamarin.Forms . **El paquete NuGet de Maps debe estar instalado. Android y Plataforma universal de Windows requieren una clave de autorización de asignación.<br /><br />[Documentación](xref:Xamarin.Forms.Maps.Map)  /  de API [Guía](~/xamarin-forms/user-interface/map/index.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) de | [![Ejemplo de mapa](views-images/Map.png "Ejemplo de mapa")](views-images/Map-Large.png#lightbox "Ejemplo de mapa")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)reproduce vídeo o audio. Los elementos multimedia se pueden reproducir desde una dirección URL o desde un archivo local, en función de si la [`Source`](xref:Xamarin.Forms.MediaElement.Source) propiedad está establecida en [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) o [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) .<br /><br />[Documentación](xref:Xamarin.Forms.MediaElement)  /  de API [Guía](~/xamarin-forms/user-interface/mediaelement.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) de | [![Ejemplo de MediaElement](views-images/MediaElement.png "Ejemplo de MediaElement")](views-images/MediaElement-Large.png#lightbox "Ejemplo de MediaElement")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)muestra los gráficos OpenGL en los proyectos de iOS y Android. No se admite el Plataforma universal de Windows. Los proyectos de iOS y Android requieren una referencia al ensamblado **OpenTK-1,0** o al ensamblado de la versión 1.0.0.0 de **OpenTK** . `OpenGLView`es más fácil de usar en un proyecto compartido; Si se utiliza en una biblioteca de .NET Standard, también se requerirá un servicio de dependencia (como se muestra en el código de ejemplo).<br /><br />Esta es la única instalación de gráficos integrada en Xamarin.Forms , pero una Xamarin.Forms aplicación también puede representar gráficos mediante [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) o [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md) .<br /><br />[Documentación de API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Ejemplo de OpenGLView](views-images/OpenGLView.png "Ejemplo de OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Ejemplo de OpenGLView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)muestra páginas web o contenido HTML, en función de si la [`Source`](xref:Xamarin.Forms.WebView.Source) propiedad está establecida en un [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) objeto o.<br /><br />[Documentación](xref:Xamarin.Forms.WebView)  /  de API [Guía](~/xamarin-forms/user-interface/webview.md)  /  de [Ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) | [![Ejemplo de WebView](views-images/WebView.png "Ejemplo de WebView")](views-images/WebView-Large.png#lightbox "Ejemplo de WebView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Vistas que inician comandos

### <a name="button"></a>Botón

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)es un objeto rectangular que muestra texto y que activa un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento cuando se presiona.<br /><br />[Documentación](xref:Xamarin.Forms.Button)  /  de API [Guía](~/xamarin-forms/user-interface/button.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) de | [![Ejemplo de botón](views-images/Button.png "Ejemplo de botón")](views-images/Button-Large.png#lightbox "Ejemplo de botón")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton`es un objeto rectangular que muestra una imagen y que activa un `Clicked` evento cuando se presiona.<br /><br /> [Guía](~/xamarin-forms/user-interface/imagebutton.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de | [![Ejemplo de ImageButton](views-images/ImageButton.png "Ejemplo de ImageButton")](views-images/ImageButton-Large.png#lightbox "Ejemplo de ImageButton")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| `RadioButton`permite la selección de una opción de un conjunto y activa un `CheckedChanged` evento cuando se produce la selección.<br /><br />[Guía](~/xamarin-forms/user-interface/radiobutton.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) de | [![Ejemplo de RadioButton](views-images/RadioButton.png "Ejemplo de RadioButton")](views-images/RadioButton-Large.png#lightbox "Ejemplo de RadioButton")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView`es un control contenedor que proporciona funcionalidad de extracción para actualizar para el contenido desplazable. El `ICommand` definido por la `Command` propiedad se ejecuta cuando se desencadena una actualización, y la `IsRefreshing` propiedad indica el estado actual del control.<br /><br /> [Guía](~/xamarin-forms/user-interface/refreshview.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de | [![Ejemplo de RefreshView](views-images/RefreshView.png "Ejemplo de RefreshView")](views-images/RefreshView-Large.png#lightbox "Ejemplo de RefreshView")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>Barra de búsqueda

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)muestra un área para que el usuario escriba una cadena de texto y un botón (o una tecla del teclado) que señale a la aplicación para realizar una búsqueda. La [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad proporciona acceso al texto y el [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica que se ha presionado el botón.<br /><br />[Documentación](xref:Xamarin.Forms.SearchBar)  /  de API [Guía](~/xamarin-forms/user-interface/searchbar.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) de | [![Ejemplo de barra](views-images/SearchBar.png "Ejemplo de barra")](views-images/SearchBar-Large.png#lightbox "Ejemplo de barra")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView`es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente. Cada elemento de menú se representa mediante un `SwipeItem` , que tiene una `Command` propiedad que ejecuta `ICommand` cuando se puntea el elemento.<br /><br /> [Guía](~/xamarin-forms/user-interface/swipeview.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de | [![Ejemplo de SwipeView](views-images/SwipeView.png "Ejemplo de SwipeView")](views-images/SwipeView-Large.png#lightbox "Ejemplo de SwipeView")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Vistas para establecer valores

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox`permite al usuario seleccionar un valor booleano mediante un tipo de botón que puede estar activado o vacío. La `IsChecked` propiedad es el estado de `CheckBox` , y el `CheckedChanged` evento se desencadena cuando cambia el estado.<br /><br />Ejemplo de documentación/ [Guía](~/xamarin-forms/user-interface/checkbox.md)de la API  /  [Sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) | [![Ejemplo de CheckBox](views-images/CheckBox.png "Ejemplo de CheckBox")](views-images/CheckBox-Large.png#lightbox "Ejemplo de CheckBox")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Control deslizante

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)permite al usuario seleccionar un `double` valor de un intervalo continuo especificado con las [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) propiedades y [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) .<br /><br />[Documentación](xref:Xamarin.Forms.Slider)  /  de API [Guía](~/xamarin-forms/user-interface/slider.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) de | [![Ejemplo de control deslizante](views-images/Slider.png "Ejemplo de control deslizante")](views-images/Slider-Large.png#lightbox "Ejemplo de control deslizante")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Control de incremento

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)permite al usuario seleccionar un `double` valor de un intervalo de valores incrementales especificado con las [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propiedades, [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) y [`Increment`](xref:Xamarin.Forms.Stepper.Increment) .<br /><br />[Documentación](xref:Xamarin.Forms.Stepper)   /  de API [Guía](~/xamarin-forms/user-interface/stepper.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) de | [![Ejemplo de stepper](views-images/Stepper.png "Ejemplo de stepper")](views-images/Stepper-Large.png#lightbox "Ejemplo de stepper")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Modificador

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)toma la forma de un modificador ON/OFF para permitir al usuario seleccionar un valor booleano. La [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propiedad es el estado del modificador y el [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento se desencadena cuando cambia el estado.<br /><br />[Documentación](xref:Xamarin.Forms.Switch)  /  de API [Guía](~/xamarin-forms/user-interface/switch.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) de | [![Ejemplo de conmutador](views-images/Switch.png "Ejemplo de conmutador")](views-images/Switch-Large.png#lightbox "Ejemplo de conmutador")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)permite al usuario seleccionar una fecha con el selector de fecha de la plataforma. Establezca un intervalo de fechas permitidas con las [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) propiedades y. La [`Date`](xref:Xamarin.Forms.DatePicker.Date) propiedad es la fecha seleccionada y el [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento se desencadena cuando cambia esa propiedad.<br /><br />[Documentación](xref:Xamarin.Forms.DatePicker)  /  de API [Guía](~/xamarin-forms/user-interface/datepicker.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) de | [![Ejemplo de DatePicker](views-images/DatePicker.png "Ejemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "Ejemplo de DatePicker")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)permite al usuario seleccionar una hora con el selector de tiempo de plataforma. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad es la hora seleccionada. Una aplicación puede supervisar los cambios en la `Time` propiedad mediante la instalación de un controlador para el [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.<br /><br />[Documentación](xref:Xamarin.Forms.TimePicker)  /  de API [Guía](~/xamarin-forms/user-interface/timepicker.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) de | [![Ejemplo de TimePicker](views-images/TimePicker.png "Ejemplo de TimePicker")](views-images/TimePicker-Large.png#lightbox "Ejemplo de TimePicker")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Vistas para editar texto

Estas dos clases se derivan de la [`InputView`](xref:Xamarin.Forms.InputView) clase, que define la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) propiedad.

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)permite al usuario escribir y editar una sola línea de texto. El texto está disponible como la [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad y los [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventos y [`Completed`](xref:Xamarin.Forms.Entry.Completed) se activan cuando cambia el texto o el usuario envía una señal de finalización pulsando la tecla entrar.<br /><br />Utilice [`Editor`](#editor) para escribir y editar varias líneas de texto.<br /><br />[Documentación](xref:Xamarin.Forms.Entry)  /  de API [Guía](~/xamarin-forms/user-interface/text/entry.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de | [![Ejemplo de entrada](views-images/Entry.png "Ejemplo de entrada")](views-images/Entry-Large.png#lightbox "Ejemplo de entrada")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)permite al usuario escribir y editar varias líneas de texto. El texto está disponible como la [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad y los [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventos y [`Completed`](xref:Xamarin.Forms.Editor.Completed) se activan cuando cambia el texto o el usuario envía una señal de finalización.<br /><br />Use una [`Entry`](#entry) vista para escribir y editar una sola línea de texto.<br /><br />[Documentación](xref:Xamarin.Forms.Editor)  /  de API [Guía](~/xamarin-forms/user-interface/text/editor.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de | [![Ejemplo de entrada](views-images/Editor.png "Ejemplo de editor")](views-images/Editor-Large.png#lightbox "Ejemplo de editor")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Vistas para indicar actividad

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)usa una animación para mostrar que la aplicación está ocupada en una actividad prolongada sin ofrecer ninguna indicación de progreso. La [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propiedad controla la animación.<br /><br />Si se conoce el progreso de la actividad, use [`ProgressBar`](#progressbar) en su lugar.<br /><br />[Documentación](xref:Xamarin.Forms.ActivityIndicator)  /  de API [Guía](~/xamarin-forms/user-interface/activityindicator.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) de | [![Ejemplo de ActivityIndicator](views-images/ActivityIndicator.png "Ejemplo de ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Ejemplo de ActivityIndicator")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)usa una animación para mostrar que la aplicación progresa a través de una actividad prolongada. Establezca la [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) propiedad en valores entre 0 y 1 para indicar el progreso.<br /><br />Si no se conoce el progreso de la actividad, use [`ActivityIndicator`](#activityindicator) en su lugar.<br /><br />[Documentación](xref:Xamarin.Forms.ProgressBar)  /  de API [Guía](~/xamarin-forms/user-interface/progressbar.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) de | [![Ejemplo de ProgressBar](views-images/ProgressBar.png "Ejemplo de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Ejemplo de ProgressBar")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Vistas que muestran colecciones

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)muestra una lista desplazable de elementos de datos. Establezca la `ItemsSource` propiedad en una colección de objetos y establezca la `ItemTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que describa cómo se va a dar formato a los elementos. El `CurrentItemChanged` evento indica que el elemento mostrado actualmente ha cambiado, que está disponible como la `CurrentItem` propiedad.<br /><br />[Guía](~/xamarin-forms/user-interface/carouselview/index.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) de | [![Ejemplo de CarouselView](views-images/CarouselView.png "Ejemplo de CarouselView")](views-images/CarouselView-Large.png#lightbox "Ejemplo de CarouselView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)muestra una lista desplazable de elementos de datos seleccionables, con distintas especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView) . Establezca la `ItemsSource` propiedad en una colección de objetos y establezca la `ItemTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que describa cómo se va a dar formato a los elementos. El `SelectionChanged` evento indica que se ha realizado una selección, que está disponible como la `SelectedItem` propiedad.<br /><br />[Guía](~/xamarin-forms/user-interface/collectionview/index.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) de | [![Ejemplo de CollectionView](views-images/CollectionView.png "Ejemplo de CollectionView")](views-images/CollectionView-Large.png#lightbox "Ejemplo de CollectionView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView`muestra indicadores que representan el número de elementos de un `CarouselView` . Establezca la `CarouselView.IndicatorView` propiedad en el `IndicatorView` objeto para mostrar indicadores para `CarouselView` . <br /><br />[Guía](~/xamarin-forms/user-interface/indicatorview.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) de | [![Ejemplo de IndicatorView](views-images/IndicatorView.png "Ejemplo de IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Ejemplo de IndicatorView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)deriva de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) y muestra una lista desplazable de elementos de datos seleccionables. Establezca la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propiedad en una colección de objetos y establezca la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que describa cómo se va a dar formato a los elementos. El [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento indica que se ha realizado una selección, que está disponible como la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propiedad.<br /><br />[Documentación](xref:Xamarin.Forms.ListView)  /  de API [Guía](~/xamarin-forms/user-interface/listview/index.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) de | [![Ejemplo de ListView](views-images/ListView.png "Ejemplo de ListView")](views-images/ListView-Large.png#lightbox "Ejemplo de ListView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Selector

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)muestra un elemento seleccionado en una lista de cadenas de texto y permite seleccionar ese elemento cuando se puntea en la vista. Establezca la [`Items`](xref:Xamarin.Forms.Picker.Items) propiedad en una lista de cadenas o la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propiedad en una colección de objetos. El [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento se desencadena cuando se selecciona un elemento.<br /><br />`Picker`Muestra la lista de elementos solo cuando está seleccionado. Use [`ListView`](#listview) o [`TableView`](#tableview) para una lista desplazable que permanezca en la página.<br /><br />[Documentación](xref:Xamarin.Forms.Picker)  /  de API [Guía](~/xamarin-forms/user-interface/picker/index.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) de | [![Ejemplo del selector](views-images/Picker.png "Ejemplo del selector")](views-images/Picker-Large.png#lightbox "Ejemplo del selector")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)muestra una lista de filas de tipo [`Cell`](xref:Xamarin.Forms.Cell) con encabezados y subencabezados opcionales. Establezca la [`Root`](xref:Xamarin.Forms.TableView.Root) propiedad en un objeto de tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot) y agregue [`TableSection`](xref:Xamarin.Forms.TableSection) objetos a esa `TableRoot` . Cada `TableSection` es una colección de `Cell` objetos.<br /><br />[Documentación](xref:Xamarin.Forms.TableView)  /  de API [Guía](~/xamarin-forms/user-interface/tableview.md)  /  de [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) de | [![Ejemplo de TableView](views-images/TableView.png "Ejemplo de TableView")](views-images/TableView-Large.png#lightbox "Ejemplo de TableView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.FormsEjemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAssembl](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentación de API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
