---
title: Características de la plataforma Android
description: En este artículo se explica cómo agregar funcionalidad específica de Android a las aplicaciones de Xamarin. Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: ce8d0b834cff5b2eee46b4ace5de4a95d196726d
ms.sourcegitcommit: a3b7e016fb25584dbf57bae89b64a9f98031e7c9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549999"
---
# <a name="android-platform-features"></a>Características de la plataforma Android

El desarrollo de aplicaciones de Xamarin. Forms para Android requiere Visual Studio. La [Página plataformas compatibles](~/get-started/supported-platforms.md) contiene más información sobre los requisitos previos.

## <a name="platform-specifics"></a>Características específicas de la plataforma

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

La siguiente funcionalidad específica de la plataforma se proporciona para las vistas, las páginas y los diseños de Xamarin. Forms en Android:

- Controlar el orden Z de los elementos visuales para determinar el orden de dibujo. Para obtener más información, consulte [elevación de VisualElement en Android](visualelement-elevation.md).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte el [modo de color heredado VisualElement en Android](legacy-color-mode.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin. Forms en Android:

- Utilizando los valores de la sombra de los botones de Android y el relleno predeterminado. Para obtener más información, consulte [botón relleno y sombras en Android](button-padding-shadow.md).
- Establecer el método de entrada de opciones del editor para el teclado en pantalla para una [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [Opciones del editor de métodos de entrada de entrada en Android](entry-ime-options.md).
- Habilitación de una sombra paralela en un `ImageButton`. Para obtener más información, vea [sombras Drop Shadows en Android](imagebutton-drop-shadow.md).
- Habilitar el desplazamiento rápido en un [`ListView`](xref:Xamarin.Forms.ListView) para obtener más información, consulte [desplazamiento rápido de ListView en Android](listview-fast-scrolling.md).
- Control de la transición que se utiliza al abrir una `SwipeView`. Para obtener más información, consulte el [modo de transición de SwipeView](swipeview-swipetransitionmode.md).
- Controlar si un [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto. Para obtener más información, vea [contenido mixto de WebView en Android](webview-mixed-content.md).
- Habilitar zoom en un [`WebView`](xref:Xamarin.Forms.WebView). Para obtener más información, vea [zoom de vista previa en Android](webview-zoom-controls.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las celdas de Xamarin. Forms en Android:

- La habilitación de [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo heredado de acciones de contexto, por lo que el menú acciones de contexto no se actualiza cuando cambia el elemento seleccionado en un [`ListView`](xref:Xamarin.Forms.ListView) . Para obtener más información, vea [acciones de contexto de ViewCell en Android](viewcell-context-actions.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin. Forms en Android:

- Establecer el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte alto de la [barra NavigationPage en Android](navigationpage-bar-height.md).
- Deshabilitar las animaciones de transición al navegar a través de páginas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, vea [animaciones de transición de páginas de TabbedPage en Android](tabbedpage-transition-animations.md).
- Habilitar el gesto de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, vea la [Página de TabbedPage deslizante en Android](tabbedpage-page-swiping.md).
- Establecer la posición de la barra de herramientas y el color en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, vea [selección de ubicación y color de la barra de herramientas TabbedPage en Android](tabbedpage-toolbar-placement-color.md).

La siguiente funcionalidad específica de la plataforma se proporciona para la clase de [`Application`](xref:Xamarin.Forms.Application) de Xamarin. Forms en Android:

- Establecer el modo de funcionamiento de un teclado en pantalla. Para obtener más información, vea [modo de entrada de teclado en Android](soft-keyboard-input-mode.md).
- Deshabilitar la [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) página eventos de ciclo de vida en pausa y reanudar, respectivamente, para las aplicaciones que usan AppCompat. Para obtener más información, vea [eventos del ciclo de vida de la página en Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Compatibilidad con la plataforma

Originalmente, el proyecto de Android de Xamarin. Forms predeterminado usaba un estilo anterior de representación de controles que era común antes de Android 5,0. Las aplicaciones compiladas con la plantilla tienen `FormsApplicationActivity` como la clase base de su actividad principal.

## <a name="material-design-via-appcompat"></a>Diseño de materiales a través de AppCompat

Los proyectos de Android de Xamarin. Forms ahora usan `FormsAppCompatActivity` como la clase base de su actividad principal. Esta clase usa las características de **AppCompat** proporcionadas por Android para implementar temas de diseño de materiales.

Para agregar temas de diseño de materiales al proyecto de Android de Xamarin. Forms, siga las [instrucciones de instalación para la compatibilidad con AppCompat](appcompat-material-design.md)

Este es el ejemplo de **todo** con el `FormsApplicationActivity`predeterminado:

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

Y este es el mismo código después de actualizar el proyecto para usar `FormsAppCompatActivity` (y agregar la información de tema adicional):

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Al usar `FormsAppCompatActivity`, las [clases base para algunos representadores personalizados de Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) serán diferentes.

## <a name="related-links"></a>Vínculos relacionados

- [Agregar compatibilidad con el diseño de materiales](appcompat-material-design.md)
