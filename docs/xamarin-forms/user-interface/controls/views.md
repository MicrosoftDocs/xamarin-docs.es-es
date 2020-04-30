---
title: Vistas de Xamarin. Forms
description: Las vistas de Xamarin. Forms son los bloques de creación de interfaces de usuario móviles multiplataforma. En este artículo se enumeran las vistas que se incluyen en Xamarin. Forms.
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/16/2020
ms.openlocfilehash: 4164941e4ed8d484699e52eece86085f1c761c91
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82516944"
---
# <a name="xamarinforms-views"></a>Vistas de Xamarin. Forms

[![Descargar el](~/media/shared/download.png) ejemplo descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Las vistas de Xamarin. Forms son los bloques de creación de interfaces de usuario móviles multiplataforma._

Las vistas son objetos de la interfaz de usuario, como etiquetas, botones y controles deslizantes que normalmente se conocen como *controles* o *widgets* en otros entornos de programación gráficos. Todas las vistas compatibles con Xamarin. Forms se derivan de la [`View`](xref:Xamarin.Forms.View) clase. Se pueden dividir en varias categorías:

## <a name="views-for-presentation"></a>Vistas para presentación

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](xref:Xamarin.Forms.BoxView)muestra un rectángulo sólido coloreado por [`Color`](xref:Xamarin.Forms.BoxView.Color) la propiedad. `BoxView`tiene una solicitud de tamaño predeterminado de 40 x 40. Para otros tamaños, asigne las [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) propiedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y.<br /><br />[API Documentation](xref:Xamarin.Forms.BoxView) / [Guide](~/xamarin-forms/user-interface/boxview.md)Guía / de documentación de API[ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration), [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/), [4](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife), [5](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)y [6](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) | [![Ejemplo de BoxView](views-images/BoxView.png "Ejemplo de BoxView")](views-images/BoxView-Large.png#lightbox "Ejemplo de BoxView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="expander"></a>Expander

|     |     |
| --- | --- |
| `Expander`proporciona un contenedor expansible para hospedar cualquier contenido y se compone de un encabezado y contenido. Establezca la `Header` propiedad en un [`View`](xref:Xamarin.Forms.View) que se mostrará como el encabezado y la `Content` propiedad en un [`View`](xref:Xamarin.Forms.View) que se mostrará cuando el encabezado se expanda mediante una derivación.<br /><br />[Guide](~/xamarin-forms/user-interface/expander.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos) de guía | [![Ejemplo de expansor](views-images/Expander.png "Ejemplo de expansor")](views-images/Expander-Large.png#lightbox "Ejemplo de expansor")<br /> [Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ExpanderDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ExpanderDemoPage.xaml) |
|     |     |

### <a name="label"></a>Etiqueta

|     |     |
| --- | --- |
| [`Label`](xref:Xamarin.Forms.Label)muestra cadenas de texto de una sola línea o bloques de texto de varias líneas, con un formato constante o variable. Establezca la [`Text`](xref:Xamarin.Forms.Label.Text) propiedad en una cadena para el formato constante o establezca la [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propiedad en un [`FormattedString`](xref:Xamarin.Forms.FormattedString) objeto para el formato de variable.<br /><br />[API Documentation](xref:Xamarin.Forms.Label) / [Guide](~/xamarin-forms/user-interface/text/label.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de guía de documentación de API | [![Ejemplo de etiqueta](views-images/Label.png "Ejemplo de etiqueta")](views-images/Label-Large.png#lightbox "Ejemplo de etiqueta")<br /> [Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Imagen

|     |     |
| --- | --- |
| [`Image`](xref:Xamarin.Forms.Image)muestra un mapa de bits. Los mapas de bits se pueden descargar a través de Web, incrustados como recursos en los proyectos comunes de proyecto o plataforma, `Stream` o bien crearse mediante un objeto .net.<br /><br />[API Documentation](xref:Xamarin.Forms.Image) / [Guide](~/xamarin-forms/user-interface/images.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages) de guía de documentación de API | [![Ejemplo de imagen](views-images/Image.png "Ejemplo de imagen")](views-images/Image-Large.png#lightbox "Ejemplo de imagen")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="map"></a>Map

|     |     |
| --- | --- |
| [`Map`](xref:Xamarin.Forms.Maps.Map)muestra un mapa. El paquete de NuGet **Xamarin. Forms. Maps** debe estar instalado. Android y Plataforma universal de Windows requieren una clave de autorización de asignación.<br /><br />[API Documentation](xref:Xamarin.Forms.Maps.Map) / [Guide](~/xamarin-forms/user-interface/map/index.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps/) de guía de documentación de API | [![Ejemplo de mapa](views-images/Map.png "Ejemplo de mapa")](views-images/Map-Large.png#lightbox "Ejemplo de mapa")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

### <a name="mediaelement"></a>MediaElement

|     |     |
| --- | --- |
| [`MediaElement`](xref:Xamarin.Forms.MediaElement)reproduce vídeo o audio. Los elementos multimedia se pueden reproducir desde una dirección URL o desde un archivo local, en [`Source`](xref:Xamarin.Forms.MediaElement.Source) función de si la propiedad [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource) está establecida [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)en o.<br /><br />[API Documentation](xref:Xamarin.Forms.MediaElement) / [Guide](~/xamarin-forms/user-interface/mediaelement.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos) de guía de documentación de API | [![Ejemplo de MediaElement](views-images/MediaElement.png "Ejemplo de MediaElement")](views-images/MediaElement-Large.png#lightbox "Ejemplo de MediaElement")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MediaElementDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MediaElementDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](xref:Xamarin.Forms.OpenGLView)muestra los gráficos OpenGL en los proyectos de iOS y Android. No se admite el Plataforma universal de Windows. Los proyectos de iOS y Android requieren una referencia al ensamblado **OpenTK-1,0** o al ensamblado de la versión 1.0.0.0 de **OpenTK** . `OpenGLView`es más fácil de usar en un proyecto compartido; Si se utiliza en una biblioteca de .NET Standard, también se requerirá un servicio de dependencia (como se muestra en el código de ejemplo).<br /><br />Esta es la única instalación de gráficos que está integrada en Xamarin. Forms, pero una aplicación de Xamarin. Forms también [`SkiaSharp`](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)puede representar [`UrhoSharp`](~/xamarin-forms/user-interface/graphics/urhosharp.md)gráficos mediante o.<br /><br />[Documentación de API](xref:Xamarin.Forms.OpenGLView)<br /><br /> | [![Ejemplo de OpenGLView](views-images/OpenGLView.png "Ejemplo de OpenGLView")](views-images/OpenGLView-Large.png#lightbox "Ejemplo de OpenGLView")<br />[Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](xref:Xamarin.Forms.WebView)muestra páginas web o contenido HTML, en función de si [`Source`](xref:Xamarin.Forms.WebView.Source) la propiedad está establecida en [`UriWebViewSource`](xref:Xamarin.Forms.UrlWebViewSource) un [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) objeto o.<br /><br />[API Documentation](xref:Xamarin.Forms.WebView) / [Guide](~/xamarin-forms/user-interface/webview.md) / [Ejemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview) y [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) de la guía de documentación de API | [![Ejemplo de WebView](views-images/WebView.png "Ejemplo de WebView")](views-images/WebView-Large.png#lightbox "Ejemplo de WebView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Vistas que inician comandos

### <a name="button"></a>Botón

|     |     |
| --- | --- |
| [`Button`](xref:Xamarin.Forms.Button)es un objeto rectangular que muestra texto y que activa un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento cuando se presiona.<br /><br />[API Documentation](xref:Xamarin.Forms.Button) / [Guide](~/xamarin-forms/user-interface/button.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos/) de guía de documentación de API | [![Ejemplo de botón](views-images/Button.png "Ejemplo de botón")](views-images/Button-Large.png#lightbox "Ejemplo de botón")<br /> [Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="imagebutton"></a>ImageButton

|     |     |
| --- | --- |
| `ImageButton`es un objeto rectangular que muestra una imagen y que activa un `Clicked` evento cuando se presiona.<br /><br /> [Guide](~/xamarin-forms/user-interface/imagebutton.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de guía | [![Ejemplo de ImageButton](views-images/ImageButton.png "Ejemplo de ImageButton")](views-images/ImageButton-Large.png#lightbox "Ejemplo de ImageButton")<br /> [Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageButtonDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageButtonDemoPage.xaml.cs) |
|     |     |

### <a name="radiobutton"></a>RadioButton

|     |     |
| --- | --- |
| `RadioButton`permite la selección de una opción de un conjunto y activa un `CheckedChanged` evento cuando se produce la selección.<br /><br />[Guide](~/xamarin-forms/user-interface/radiobutton.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/) de guía | [![Ejemplo de RadioButton](views-images/RadioButton.png "Ejemplo de RadioButton")](views-images/RadioButton-Large.png#lightbox "Ejemplo de RadioButton")<br /> [Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RadioButtonDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RadioButtonDemoPage.xaml.cs) |
|     |     |

### <a name="refreshview"></a>RefreshView

|     |     |
| --- | --- |
| `RefreshView`es un control contenedor que proporciona funcionalidad de extracción para actualizar para el contenido desplazable. El `ICommand` definido por la `Command` propiedad se ejecuta cuando se desencadena una actualización, y la `IsRefreshing` propiedad indica el estado actual del control.<br /><br /> [Guide](~/xamarin-forms/user-interface/refreshview.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de guía | [![Ejemplo de RefreshView](views-images/RefreshView.png "Ejemplo de RefreshView")](views-images/RefreshView-Large.png#lightbox "Ejemplo de RefreshView")<br /> [Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RefreshViewDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RefreshViewDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>Barra de búsqueda

|     |     |
| --- | --- |
| [`SearchBar`](xref:Xamarin.Forms.SearchBar)muestra un área para que el usuario escriba una cadena de texto y un botón (o una tecla del teclado) que señale a la aplicación para realizar una búsqueda. La [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad proporciona acceso al texto y el [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) evento indica que se ha presionado el botón.<br /><br />[API Documentation](xref:Xamarin.Forms.SearchBar) / [Guide](~/xamarin-forms/user-interface/searchbar.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/) de guía de documentación de API | [![Ejemplo de barra](views-images/SearchBar.png "Ejemplo de barra")](views-images/SearchBar-Large.png#lightbox "Ejemplo de barra")<br /> [Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

### <a name="swipeview"></a>SwipeView

|     |     |
| --- | --- |
| `SwipeView`es un control contenedor que se ajusta alrededor de un elemento de contenido y proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente. Cada elemento de menú se representa mediante `SwipeItem`un, que tiene `Command` una propiedad que ejecuta `ICommand` cuando se puntea el elemento.<br /><br /> [Guide](~/xamarin-forms/user-interface/swipeview.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) de guía | [![Ejemplo de SwipeView](views-images/SwipeView.png "Ejemplo de SwipeView")](views-images/SwipeView-Large.png#lightbox "Ejemplo de SwipeView")<br /> [Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwipeViewDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwipeViewDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Vistas para establecer valores

### <a name="checkbox"></a>CheckBox

|     |     |
| --- | --- |
| `CheckBox`permite al usuario seleccionar un valor booleano mediante un tipo de botón que puede estar activado o vacío. La `IsChecked` propiedad es el estado de `CheckBox`, y el `CheckedChanged` evento se desencadena cuando cambia el estado.<br /><br />[Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos) de documentación/ [Guía](~/xamarin-forms/user-interface/checkbox.md) / de la API | [![Ejemplo de CheckBox](views-images/CheckBox.png "Ejemplo de CheckBox")](views-images/CheckBox-Large.png#lightbox "Ejemplo de CheckBox")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CheckBoxPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CheckBoxPage.xaml) |
|     |     |

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](xref:Xamarin.Forms.Slider)permite al usuario seleccionar un `double` valor de un intervalo continuo especificado con las [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) propiedades y [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) .<br /><br />[API Documentation](xref:Xamarin.Forms.Slider) / [Guide](~/xamarin-forms/user-interface/slider.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) de guía de documentación de API | [![Ejemplo de control deslizante](views-images/Slider.png "Ejemplo de control deslizante")](views-images/Slider-Large.png#lightbox "Ejemplo de control deslizante")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Control de incremento

|     |     |
| --- | --- |
| [`Stepper`](xref:Xamarin.Forms.Stepper)permite al usuario seleccionar `double` un valor de un intervalo de valores incrementales especificado con las [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)propiedades, [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)y. [`Increment`](xref:Xamarin.Forms.Stepper.Increment)<br /><br />[API Documentation](xref:Xamarin.Forms.Stepper)  / [Guide](~/xamarin-forms/user-interface/stepper.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) de guía de documentación de API | [![Ejemplo de stepper](views-images/Stepper.png "Ejemplo de stepper")](views-images/Stepper-Large.png#lightbox "Ejemplo de stepper")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Switch

|     |     |
| --- | --- |
| [`Switch`](xref:Xamarin.Forms.Switch)toma la forma de un modificador ON/OFF para permitir al usuario seleccionar un valor booleano. La [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propiedad es el estado del modificador y el [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento se desencadena cuando cambia el estado.<br /><br />[API Documentation](xref:Xamarin.Forms.Switch) / [Guide](~/xamarin-forms/user-interface/switch.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/) de guía de documentación de API | [![Ejemplo de conmutador](views-images/Switch.png "Ejemplo de conmutador")](views-images/Switch-Large.png#lightbox "Ejemplo de conmutador")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](xref:Xamarin.Forms.DatePicker)permite al usuario seleccionar una fecha con el selector de fecha de la plataforma. Establezca un intervalo de fechas permitidas con las [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) propiedades [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) y. La [`Date`](xref:Xamarin.Forms.DatePicker.Date) propiedad es la fecha seleccionada y el [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento se desencadena cuando cambia esa propiedad.<br /><br />[API Documentation](xref:Xamarin.Forms.DatePicker) / [Guide](~/xamarin-forms/user-interface/datepicker.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) de guía de documentación de API | [![Ejemplo de DatePicker](views-images/DatePicker.png "Ejemplo de DatePicker")](views-images/DatePicker-Large.png#lightbox "Ejemplo de DatePicker")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](xref:Xamarin.Forms.TimePicker)permite al usuario seleccionar una hora con el selector de tiempo de plataforma. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) propiedad es la hora seleccionada. Una aplicación puede supervisar los cambios en `Time` la propiedad mediante la instalación de un [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) controlador para el evento.<br /><br />[API Documentation](xref:Xamarin.Forms.TimePicker) / [Guide](~/xamarin-forms/user-interface/timepicker.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) de guía de documentación de API | [![Ejemplo de TimePicker](views-images/TimePicker.png "Ejemplo de TimePicker")](views-images/TimePicker-Large.png#lightbox "Ejemplo de TimePicker")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Vistas para editar texto

Estas dos clases se derivan [`InputView`](xref:Xamarin.Forms.InputView) de la clase, que [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) define la propiedad.

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](xref:Xamarin.Forms.Entry)permite al usuario escribir y editar una sola línea de texto. El texto está disponible como la [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad y los [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventos y [`Completed`](xref:Xamarin.Forms.Entry.Completed) se activan cuando cambia el texto o el usuario envía una señal de finalización pulsando la tecla entrar.<br /><br />Utilice [`Editor`](#editor) para escribir y editar varias líneas de texto.<br /><br />[API Documentation](xref:Xamarin.Forms.Entry) / [Guide](~/xamarin-forms/user-interface/text/entry.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de guía de documentación de API | [![Ejemplo de entrada](views-images/Entry.png "Ejemplo de entrada")](views-images/Entry-Large.png#lightbox "Ejemplo de entrada")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](xref:Xamarin.Forms.Editor)permite al usuario escribir y editar varias líneas de texto. El texto está disponible como la [`Text`](xref:Xamarin.Forms.InputView.Text) propiedad y los [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) eventos y [`Completed`](xref:Xamarin.Forms.Editor.Completed) se activan cuando cambia el texto o el usuario envía una señal de finalización.<br /><br />Use una [`Entry`](#entry) vista para escribir y editar una sola línea de texto.<br /><br />[API Documentation](xref:Xamarin.Forms.Editor) / [Guide](~/xamarin-forms/user-interface/text/editor.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text) de guía de documentación de API | [![Ejemplo de entrada](views-images/Editor.png "Ejemplo de editor")](views-images/Editor-Large.png#lightbox "Ejemplo de editor")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Vistas para indicar actividad

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)usa una animación para mostrar que la aplicación está ocupada en una actividad prolongada sin ofrecer ninguna indicación de progreso. La [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propiedad controla la animación.<br /><br />Si se conoce el progreso de la actividad, use [`ProgressBar`](#progressbar) en su lugar.<br /><br />[API Documentation](xref:Xamarin.Forms.ActivityIndicator) / [Guide](~/xamarin-forms/user-interface/activityindicator.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/) de guía de documentación de API | [![Ejemplo de ActivityIndicator](views-images/ActivityIndicator.png "Ejemplo de ActivityIndicator")](views-images/ActivityIndicator-Large.png#lightbox "Ejemplo de ActivityIndicator")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)usa una animación para mostrar que la aplicación progresa a través de una actividad prolongada. Establezca la [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) propiedad en valores entre 0 y 1 para indicar el progreso.<br /><br />Si no se conoce el progreso de la actividad, use [`ActivityIndicator`](#activityindicator) en su lugar.<br /><br />[API Documentation](xref:Xamarin.Forms.ProgressBar) / [Guide](~/xamarin-forms/user-interface/progressbar.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/) de guía de documentación de API | [![Ejemplo de ProgressBar](views-images/ProgressBar.png "Ejemplo de ProgressBar")](views-images/ProgressBar-Large.png#lightbox "Ejemplo de ProgressBar")<br />[Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Vistas que muestran colecciones

### <a name="carouselview"></a>CarouselView

|     |     |
| --- | --- |
| [`CarouselView`](xref:Xamarin.Forms.CarouselView)muestra una lista desplazable de elementos de datos. Establezca la `ItemsSource` propiedad en una colección de objetos y establezca la `ItemTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que describa cómo se va a dar formato a los elementos. El `CurrentItemChanged` evento indica que el elemento mostrado actualmente ha cambiado, que está disponible como la `CurrentItem` propiedad.<br /><br />[Guide](~/xamarin-forms/user-interface/carouselview/index.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/) de guía | [![Ejemplo de CarouselView](views-images/CarouselView.png "Ejemplo de CarouselView")](views-images/CarouselView-Large.png#lightbox "Ejemplo de CarouselView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselViewDemoPage.xaml) |
|     |     |

### <a name="collectionview"></a>CollectionView

|     |     |
| --- | --- |
| [`CollectionView`](xref:Xamarin.Forms.CollectionView)muestra una lista desplazable de elementos de datos seleccionables, con distintas especificaciones de diseño. Pretende proporcionar una alternativa más flexible y de rendimiento a [`ListView`](xref:Xamarin.Forms.ListView). Establezca la `ItemsSource` propiedad en una colección de objetos y establezca la `ItemTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que describa cómo se va a dar formato a los elementos. El `SelectionChanged` evento indica que se ha realizado una selección, que está disponible como la `SelectedItem` propiedad.<br /><br />[Guide](~/xamarin-forms/user-interface/collectionview/index.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/) de guía | [![Ejemplo de CollectionView](views-images/CollectionView.png "Ejemplo de CollectionView")](views-images/CollectionView-Large.png#lightbox "Ejemplo de CollectionView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CollectionViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CollectionViewDemoPage.xaml) |
|     |     |

### <a name="indicatorview"></a>IndicatorView

|     |     |
| --- | --- |
| `IndicatorView`muestra indicadores que representan el número de elementos de un `CarouselView`. Establezca la `CarouselView.IndicatorView` propiedad en el `IndicatorView` objeto para mostrar indicadores para `CarouselView`. <br /><br />[Guide](~/xamarin-forms/user-interface/indicatorview.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/) de guía | [![Ejemplo de IndicatorView](views-images/IndicatorView.png "Ejemplo de IndicatorView")](views-images/IndicatorView-Large.png#lightbox "Ejemplo de IndicatorView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/IndicatorViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/IndicatorViewDemoPage.xaml) |
|     |     |

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](xref:Xamarin.Forms.ListView)deriva de [`ItemsView`](xref:Xamarin.Forms.ItemsView`1) y muestra una lista desplazable de elementos de datos seleccionables. Establezca la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propiedad en una colección de objetos y establezca la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objeto que describa cómo se va a dar formato a los elementos. El [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento indica que se ha realizado una selección, que está disponible como la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propiedad.<br /><br />[API Documentation](xref:Xamarin.Forms.ListView) / [Guide](~/xamarin-forms/user-interface/listview/index.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview/) de guía de documentación de API | [![Ejemplo de ListView](views-images/ListView.png "Ejemplo de ListView")](views-images/ListView-Large.png#lightbox "Ejemplo de ListView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

### <a name="picker"></a>Selector

|     |     |
| --- | --- |
| [`Picker`](xref:Xamarin.Forms.Picker)muestra un elemento seleccionado en una lista de cadenas de texto y permite seleccionar ese elemento cuando se puntea en la vista. Establezca la [`Items`](xref:Xamarin.Forms.Picker.Items) propiedad en una lista de cadenas o la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propiedad en una colección de objetos. El [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento se desencadena cuando se selecciona un elemento.<br /><br />`Picker` Muestra la lista de elementos solo cuando está seleccionado. Use [`ListView`](#listview) o [`TableView`](#tableview) para una lista desplazable que permanezca en la página.<br /><br />[API Documentation](xref:Xamarin.Forms.Picker) / [Guide](~/xamarin-forms/user-interface/picker/index.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo) de guía de documentación de API | [![Ejemplo del selector](views-images/Picker.png "Ejemplo del selector")](views-images/Picker-Large.png#lightbox "Ejemplo del selector")<br />[Código de C# para esta](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / página[XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) de página con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](xref:Xamarin.Forms.TableView)muestra una lista de filas de tipo [`Cell`](xref:Xamarin.Forms.Cell) con encabezados y subencabezados opcionales. Establezca la [`Root`](xref:Xamarin.Forms.TableView.Root) propiedad en un objeto de tipo [`TableRoot`](xref:Xamarin.Forms.TableRoot)y agregue [`TableSection`](xref:Xamarin.Forms.TableSection) objetos a esa `TableRoot`. Cada `TableSection` es una colección de `Cell` objetos.<br /><br />[API Documentation](xref:Xamarin.Forms.TableView) / [Guide](~/xamarin-forms/user-interface/tableview.md) / [Ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview) de guía de documentación de API | [![Ejemplo de TableView](views-images/TableView.png "Ejemplo de TableView")](views-images/TableView-Large.png#lightbox "Ejemplo de TableView")<br />[Código C# para esta página XAML de la página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewFormDemoPage.cs) / [XAML page](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewFormDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Ejemplos de Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
