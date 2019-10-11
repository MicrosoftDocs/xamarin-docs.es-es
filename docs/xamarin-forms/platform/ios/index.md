---
title: características de la plataforma iOS en Xamarin. Forms
description: Agregar funcionalidad específica de iOS a aplicaciones de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2019
ms.openlocfilehash: c90cfc297914b585403ae84e7dbac11fd6e02836
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170945"
---
# <a name="ios-platform-features-in-xamarinforms"></a>características de la plataforma iOS en Xamarin. Forms

El desarrollo de aplicaciones de Xamarin. Forms para iOS requiere Visual Studio. La [Página requisitos](~/get-started/requirements.md) contiene más información sobre los requisitos previos.

## <a name="platform-specifics"></a>Características específicas de la plataforma

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

La siguiente funcionalidad específica de la plataforma se proporciona para las vistas, las páginas y los diseños de Xamarin. Forms en iOS:

- Desenfoque ninguna compatibilidad con [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement Blur in iOS](visualelement-blur.md).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte el [modo de color heredado VisualElement en iOS](legacy-color-mode.md).
- Habilitación de una sombra paralela en un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement Drop Shadows on IOS](visualelement-drop-shadow.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin. Forms en iOS:

- Establecer el color [de fondo de `Cell`](xref:Xamarin.Forms.Cell) . Para obtener más información, vea [color de fondo de celda en iOS](cell-background-color.md).
- Asegurarse de que especifica el texto se ajusta a un [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando el tamaño de fuente. Para obtener más información, vea [tamaño de fuente de entrada en iOS](entry-font-size.md).
- Establecer el color de cursor de un [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, vea [color cursor entry in iOS](entry-cursor-color.md).
- Controlar si las celdas [de encabezado de `ListView`](xref:Xamarin.Forms.ListView) flotan durante el desplazamiento. Para obtener más información, consulte [estilo de encabezado de grupo de ListView en iOS](listview-group-header-style.md).
- Controlar si las animaciones de fila están deshabilitadas cuando se está actualizando la colección [de elementos @no__t 1](xref:Xamarin.Forms.ListView) . Para obtener más información, consulte [animaciones de filas de ListView en iOS](listview-row-animations.md).
- Establecer el estilo del separador en un [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [estilo de separador de ListView en iOS](listview-separator-style.md).
- Controlar cuándo se produce la selección de elementos en un [ `Picker` ](xref:Xamarin.Forms.Picker). Para obtener más información, consulte [selección de elementos de selector en iOS](picker-selection.md).
- Habilitar la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad debe establecerse si pulsa en una posición en la [ `Slider` ](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar la `Slider` thumb. Para obtener más información, consulte [Slide Thumb TAP on IOS](slider-thumb.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin. Forms en iOS:

- Ocultar el separador de barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte [separador de la barra NavigationPage en iOS](navigation-bar-separator.md).
- Controlar si la barra de navegación es translúcida. Para obtener más información, consulte [barra de navegación translucidez en iOS](navigation-bar-translucent.md).
- Controlar si el texto de la barra de estado de color en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación. Para obtener más información, consulte el [modo de color de texto de la barra NavigationPage en iOS](status-bar-text-color.md).
- Controlar si el título de página se muestra como un título en la barra de navegación de página grande. Para obtener más información, vea [títulos de páginas grandes en iOS](page-large-title.md).
- Establecer la visibilidad del indicador de inicio en un [`Page`](xref:Xamarin.Forms.Page). Para obtener más información, consulte [visibilidad del indicador de inicio en iOS](page-home-indicator.md).
- Establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page). Para obtener más información, vea [visibilidad de la barra de estado de la página en iOS](page-status-bar-visibility.md).
- Garantizar ese contenido de la página se coloca en un área de la pantalla que sea seguro para todos los dispositivos iOS. Para obtener más información, consulte [Guía de diseño de área segura en iOS](page-safe-area-layout.md).
- Establecer el estilo de presentación de las páginas modales. Para obtener más información, consulte el [estilo de presentación de página modal](page-presentation-style.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para los diseños de Xamarin. Forms en iOS:

- Controlar si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) controla un movimiento táctil o pasa a su contenido. Para obtener más información, consulte [ScrollView Content toques in iOS](scrollview-content-touches.md).

La siguiente funcionalidad específica de la plataforma se proporciona para la clase de Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application) en iOS:

- Deshabilitar el ajuste de escala de accesibilidad para tamaños de fuente con nombre. Para obtener más información, vea [escalado de accesibilidad para tamaños de fuente con nombre en iOS](named-font-size-scaling.md).
- Habilitación de diseño del control y la representación de las actualizaciones que se realizará en el subproceso principal. Para obtener más información, vea [actualizaciones principales del control de subprocesos en iOS](main-thread-updates-ui.md).
- Habilitar un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) en una vista desplazable para capturar y compartir el movimiento panorámico con la vista de desplazamiento. Para obtener más información, consulte [reconocimiento simultáneo de gestos de pan en iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formato específico de iOS

Xamarin. Forms permite establecer colores y estilos de la interfaz de usuario multiplataforma, pero hay otras opciones para configurar el tema de iOS con las API de la plataforma en el proyecto de iOS.

[Obtenga más](formatting.md) información sobre cómo dar formato a la interfaz de usuario con las API específicas de iOS, como la configuración de **info. plist** y la API `UIAppearance`.

![](images/status-white-sml.png "iOS")

## <a name="other-ios-features"></a>Otras características de iOS

Con los [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)y [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), es posible incorporar una amplia variedad de funciones nativas en aplicaciones de Xamarin. Forms para iOS.
