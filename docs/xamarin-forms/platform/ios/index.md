---
title: características de la plataforma iOS en Xamarin. Forms
description: Agregar funcionalidad específica de iOS a aplicaciones de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: 97b9b70a1df61beb7b064c99721b4277e2570b41
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517056"
---
# <a name="ios-platform-features-in-xamarinforms"></a>características de la plataforma iOS en Xamarin. Forms

El desarrollo de aplicaciones de Xamarin. Forms para iOS requiere Visual Studio. La [Página plataformas compatibles](~/get-started/supported-platforms.md) contiene más información sobre los requisitos previos.

## <a name="platform-specifics"></a>Características específicas de las plataformas

Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados.

La siguiente funcionalidad específica de la plataforma se proporciona para las vistas, las páginas y los diseños de Xamarin. Forms en iOS:

- Desenfocar la [`VisualElement`](xref:Xamarin.Forms.VisualElement)compatibilidad con any. Para obtener más información, consulte [VisualElement Blur in iOS](visualelement-blur.md).
- Deshabilitar el modo de color heredado [`VisualElement`](xref:Xamarin.Forms.VisualElement)en un compatible. Para obtener más información, consulte el [modo de color heredado VisualElement en iOS](legacy-color-mode.md).
- Habilitar una sombra paralela en un [`VisualElement`](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement Drop Shadows on IOS](visualelement-drop-shadow.md).
- Habilitar un [`VisualElement`](xref:Xamarin.Forms.VisualElement) objeto para que se convierta en el primer respondedor para tocar eventos. Para obtener más información, consulte [VisualElement First respondedor](visualelement-first-responder.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin. Forms en iOS:

- Establecer el [`Cell`](xref:Xamarin.Forms.Cell) color de fondo. Para obtener más información, vea [color de fondo de celda en iOS](cell-background-color.md).
- Controlar cuándo se realiza la [`DatePicker`](xref:Xamarin.Forms.DatePicker)selección de elementos en. Para obtener más información, consulte [selección de elementos del DatePicker en iOS](datepicker-selection.md).
- Asegurarse de que el texto introducido quepa en un [`Entry`](xref:Xamarin.Forms.Entry) ajustando el tamaño de la fuente. Para obtener más información, vea [tamaño de fuente de entrada en iOS](entry-font-size.md).
- Establecer el color del cursor en [`Entry`](xref:Xamarin.Forms.Entry)un. Para obtener más información, vea [color cursor entry in iOS](entry-cursor-color.md).
- Controlar si [`ListView`](xref:Xamarin.Forms.ListView) las celdas de encabezado flotan durante el desplazamiento. Para obtener más información, consulte [estilo de encabezado de grupo de ListView en iOS](listview-group-header-style.md).
- Controlar si se deshabilitan las animaciones [`ListView`](xref:Xamarin.Forms.ListView) de filas cuando se actualiza la colección de elementos. Para obtener más información, consulte [animaciones de filas de ListView en iOS](listview-row-animations.md).
- Establecer el estilo del separador [`ListView`](xref:Xamarin.Forms.ListView)en un. Para obtener más información, consulte [estilo de separador de ListView en iOS](listview-separator-style.md).
- Controlar cuándo se realiza la [`Picker`](xref:Xamarin.Forms.Picker)selección de elementos en. Para obtener más información, consulte [selección de elementos de selector en iOS](picker-selection.md).
- Controlar si un [`SearchBar`](xref:Xamarin.Forms.SearchBar) tiene un fondo. Para obtener más información, consulte [estilo de barra en iOS](searchbar-style.md).
- Habilitar la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propiedad para establecerla punteando en una posición de la [`Slider`](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar el `Slider` control. Para obtener más información, consulte [Slide Thumb TAP on IOS](slider-thumb.md).
- Control de la transición que se utiliza al abrir `SwipeView`. Para obtener más información, consulte el [modo de transición de SwipeView](swipeview-swipetransitionmode.md).
- Controlar cuándo se realiza la [`TimePicker`](xref:Xamarin.Forms.TimePicker)selección de elementos en. Para obtener más información, vea [selección de elementos de TimePicker en iOS](timepicker-selection.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin. Forms en iOS:

- Controlar si la página de detalles de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) una tiene una sombra aplicada, al revelar la página maestra. Para obtener más información, consulte [MasterDetailPage Shadow](masterdetailpage-shadow.md).
- Ocultar el separador de barra de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)navegación en un. Para obtener más información, consulte [separador de la barra NavigationPage en iOS](navigation-bar-separator.md).
- Controlar si la barra de navegación es traslúcida. Para obtener más información, consulte [barra de navegación translucidez en iOS](navigation-bar-translucent.md).
- Controlar si el color del texto de la barra [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) de estado en un se ajusta para que coincida con la luminosidad de la barra de navegación. Para obtener más información, consulte el [modo de color de texto de la barra NavigationPage en iOS](status-bar-text-color.md).
- Controlar si el título de la página se muestra como un título grande en la barra de navegación de la página. Para obtener más información, vea [títulos de páginas grandes en iOS](page-large-title.md).
- Establecer la visibilidad del indicador de inicio en un [`Page`](xref:Xamarin.Forms.Page). Para obtener más información, consulte [visibilidad del indicador de inicio en iOS](page-home-indicator.md).
- Establecer la visibilidad de la barra de [`Page`](xref:Xamarin.Forms.Page)estado en un. Para obtener más información, vea [visibilidad de la barra de estado de la página en iOS](page-status-bar-visibility.md).
- Asegurarse de que el contenido de la página se coloca en un área de la pantalla que es segura para todos los dispositivos iOS. Para obtener más información, consulte [Guía de diseño de área segura en iOS](page-safe-area-layout.md).
- Establecer el estilo de presentación de las páginas modales. Para obtener más información, consulte el [estilo de presentación de página modal](page-presentation-style.md).
- Establecer el modo translucidez de la barra de pestañas [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)en un. Para obtener más información, vea [TabbedPage translúcida con tabulación en iOS](tabbedpage-translucent-tabbar.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para los diseños de Xamarin. Forms en iOS:

- Controlar si un [`ScrollView`](xref:Xamarin.Forms.ScrollView) control controla un gesto táctil o lo pasa a su contenido. Para obtener más información, consulte [ScrollView Content toques in iOS](scrollview-content-touches.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para la clase de [`Application`](xref:Xamarin.Forms.Application) Xamarin. Forms en iOS:

- Deshabilitar el ajuste de escala de accesibilidad para tamaños de fuente con nombre. Para obtener más información, vea [escalado de accesibilidad para tamaños de fuente con nombre en iOS](named-font-size-scaling.md).
- Habilitar las actualizaciones de diseño y representación de controles que se van a realizar en el subproceso principal. Para obtener más información, vea [actualizaciones principales del control de subprocesos en iOS](main-thread-updates-ui.md).
- Habilitar un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) en una vista de desplazamiento para capturar y compartir el gesto de panorámica con la vista de desplazamiento. Para obtener más información, consulte [reconocimiento simultáneo de gestos de pan en iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formato específico de iOS

Xamarin. Forms permite establecer colores y estilos de la interfaz de usuario multiplataforma, pero hay otras opciones para configurar el tema de iOS con las API de la plataforma en el proyecto de iOS.

[Obtenga más](formatting.md) información sobre cómo dar formato a la interfaz de usuario con las API específicas de iOS, como la configuración `UIAppearance` de **info. plist** y la API.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Otras características de iOS

Con los [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)y [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), es posible incorporar una amplia variedad de funciones nativas en aplicaciones de Xamarin. Forms para iOS.
