---
title: Controles de XAML
description: A todas las vistas que se definen en se Xamarin.Forms puede hacer referencia desde archivos XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 445ef85f661d945bda25203f35dea787e64dc9b0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138429"
---
# <a name="xaml-controls"></a>Controles de XAML

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

Las vistas son objetos de la interfaz de usuario, como etiquetas, botones y controles deslizantes que normalmente se conocen como *controles* o *widgets* en otros entornos de programación gráficos. Las vistas admitidas por Xamarin.Forms todas derivan de la [`View`](xref:Xamarin.Forms.View) clase.

A todas las vistas que se definen en se Xamarin.Forms puede hacer referencia desde archivos XAML.

## <a name="views-for-presentation"></a>Vistas para presentación

|     |     |
| --- | --- |
| <h3>BoxView</h3>Muestra un rectángulo de un color determinado.<p align="center">![Captura de pantalla de un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView)  /  de [Guía](~/xamarin-forms/user-interface/boxview.md) de | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Expander</h3>Proporciona un contenedor expansible para hospedar cualquier contenido.<p align="center">![Captura de pantalla de un expansor](xaml-controls-images/Expander.png "Expander")</p>[Guíe](~/xamarin-forms/user-interface/expander.md) | <pre>&lt;Expander&gt;<br />    &lt;Expander.Header&gt;<br />        &lt;Label Text=&quot;Baboon&quot; /&gt;<br />    &lt;/Expander.Header&gt;<br />    &lt;Image Source=&quot;Baboon.png&quot;<br />           Aspect=&quot;AspectFill&quot; /&gt;<br />&lt;/Expander&gt;</pre></p> |
| <h3>Imagen</h3>Muestra un mapa de bits.<p align="center">![Captura de pantalla de una imagen](xaml-controls-images/Image.png "Imagen")</p>[API](xref:Xamarin.Forms.Image)  /  de [Guía](~/xamarin-forms/user-interface/images.md) de | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Etiqueta</h3>Muestra una o varias líneas de texto.<p align="center">![Captura de pantalla de una etiqueta](xaml-controls-images/Label.png "Etiqueta")</p>[API](xref:Xamarin.Forms.Label)  /  de [Guía](~/xamarin-forms/user-interface/text/label.md) de | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Map</h3>Muestra un mapa.<p align="center">![Captura de pantalla de un mapa](xaml-controls-images/Map.png "Asignación")</p>[API](xref:Xamarin.Forms.Maps.Map)  /  de [Guía](~/xamarin-forms/user-interface/map/index.md) de | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>Reproducir vídeo o audio.<p align="center">![Captura de pantalla de un MediaElement](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement)  /  de [Guía](~/xamarin-forms/user-interface/mediaelement.md) de | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>WebView</h3>Muestra páginas web o contenido HTML.<p align="center">![Captura de pantalla de un WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView)  /  de [Guía](~/xamarin-forms/user-interface/webview.md) de | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Vistas que inician comandos

|     |     |
| --- | --- |
| <h3>Botón</h3>Muestra texto en un objeto rectangular.<p align="center">![Captura de pantalla de un botón](xaml-controls-images/Button.png "Botón")</p>[API](xref:Xamarin.Forms.Button)  /  de [Guía](~/xamarin-forms/user-interface/button.md) de | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Muestra una imagen en un objeto rectangular.<p align="center">![Captura de pantalla de un ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton)  /  de [Guía](~/xamarin-forms/user-interface/imagebutton.md) de | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RadioButton</h3>Permite la selección de una opción de un conjunto.<p align="center">![Captura de pantalla de un RadioButton](xaml-controls-images/RadioButton.png "RadioButton")</p>[Guíe](~/xamarin-forms/user-interface/radiobutton.md) | <p valign="center"><pre>&lt;RadioButton Text=&quot;Pineapple&quot;<br/>             CheckedChanged=&quot;OnRadioButtonCheckedChanged&quot; /&gt;</pre></p> |
| <h3>RefreshView</h3>Proporciona funcionalidad de extracción a actualización para el contenido desplazable.<p align="center">![Captura de pantalla de un RefreshView](xaml-controls-images/RefreshView.png "RefreshView")</p>[Guíe](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>Barra de búsqueda</h3> Acepta los datos proporcionados por el usuario que usa para realizar una búsqueda.<p align="center">![Captura de pantalla de un barra](xaml-controls-images/SearchBar.png "Barra de búsqueda")</p>[Guíe](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder=&quot;Enter search term&quot;<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> Proporciona elementos de menú contextual que se revelan mediante un gesto de deslizar rápidamente.<p align="center">![Captura de pantalla de un SwipeView](xaml-controls-images/SwipeView.png "SwipeView")</p>[Guíe](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Vistas para establecer valores

|     |     |
| --- | --- |
| <h3>CheckBox</h3>Permite la selección de un `boolean` valor.<p align="center">![Captura de pantalla de una casilla](xaml-controls-images/CheckBox.png "CheckBox")</p> [Guíe](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Slider</h3>Permite la selección de un `double` valor de un intervalo continuo.<p align="center">![Captura de pantalla de un control deslizante](xaml-controls-images/Slider.png "Slider")</p>[API](xref:Xamarin.Forms.Slider)  /  de [Guía](~/xamarin-forms/user-interface/slider.md) de | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Control de incremento</h3>Permite la selección de un `double` valor de un intervalo incremental.<p align="center">![Captura de pantalla de un stepper](xaml-controls-images/Stepper.png "Control de incremento")</p>[API](xref:Xamarin.Forms.Stepper)  /  de [Guía](~/xamarin-forms/user-interface/stepper.md) de | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Modificador</h3>Permite la selección de un `boolean` valor.<p align="center">![Captura de pantalla de un conmutador](xaml-controls-images/Switch.png "Modificador")</p>[API](xref:Xamarin.Forms.Switch)  /  de [Guía](~/xamarin-forms/user-interface/switch.md) de| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Permite seleccionar una fecha.<p align="center">![Captura de pantalla de un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker)  /  de [Guía](~/xamarin-forms/user-interface/datepicker.md) de | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Permite la selección de una vez.<p align="center">![Captura de pantalla de un TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker)  /  de [Guía](~/xamarin-forms/user-interface/timepicker.md) de | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Vistas para editar texto

|     |     |
| --- | --- |
| <h3>Entrada</h3>Permite escribir y editar una sola línea de texto.<p align="center">![Captura de pantalla de una entrada](xaml-controls-images/Entry.png "Entrada")</p>[API](xref:Xamarin.Forms.Entry)  /  de [Guía](~/xamarin-forms/user-interface/text/entry.md) de | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Permite escribir y editar varias líneas de texto.<p align="center">![Captura de pantalla de un editor](xaml-controls-images/Editor.png "Etiqueta")</p>[API](xref:Xamarin.Forms.Editor)  /  de [Guía](~/xamarin-forms/user-interface/text/editor.md) de | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Vistas para indicar actividad

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Muestra una animación para mostrar que la aplicación está ocupada en una actividad prolongada, sin indicar ningún progreso.<p align="center">![Captura de pantalla de un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator)  /  de [Guía](~/xamarin-forms/user-interface/activityindicator.md) de | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Muestra una animación para mostrar que la aplicación progresa a través de una actividad prolongada.<p align="center">![Captura de pantalla de un ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar)  /  de [Guía](~/xamarin-forms/user-interface/progressbar.md) de | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Vistas que muestran colecciones

|     |     |
| --- | --- |
| <h3>CarouselView</h3>Muestra una lista desplazable de elementos de datos.<p align="center">![Captura de pantalla de un CarouselView](xaml-controls-images/CarouselView.png "CarouselView")</p>[Guíe](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>Muestra una lista desplazable de elementos de datos seleccionables, con distintas especificaciones de diseño.<p align="center">![Captura de pantalla de una CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guíe](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>IndicatorView</h3>Muestra indicadores que representan el número de elementos de un `CarouselView` .<p align="center">![Captura de pantalla de un IndicatorView](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[Guíe](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>Muestra una lista desplazable de elementos de datos seleccionables.<p align="center">![Captura de pantalla de un control ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView)  /  de [Guía](~/xamarin-forms/user-interface/listview/index.md) de | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Selector</h3>Muestra un elemento seleccionado en una lista de cadenas de texto.<p align="center">![Captura de pantalla de un selector](xaml-controls-images/Picker.png "Selector")</p>[API](xref:Xamarin.Forms.Picker)  /  de [Guía](~/xamarin-forms/user-interface/picker/index.md) de | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Muestra una lista de filas interactivas.<p align="center">![Captura de pantalla de un TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView)  /  de [Guía](~/xamarin-forms/user-interface/tableview.md) de | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.FormsEjemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Ejemplos de Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentación de API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
