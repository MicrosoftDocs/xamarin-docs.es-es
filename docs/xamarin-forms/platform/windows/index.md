---
title: Características de la plataforma Windows
description: En este artículo se explica la compatibilidad con la plataforma Windows que está disponible en Xamarin.Forms .
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5f65deaad97bd69641fa4cc577d9a476106ac3a
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940361"
---
# <a name="windows-platform-features"></a>Características de la plataforma Windows

El desarrollo Xamarin.Forms de aplicaciones para plataformas Windows requiere Visual Studio. La [Página plataformas compatibles](~/get-started/supported-platforms.md) contiene más información sobre los requisitos previos.

![:: no-LOC (Xamarin. Forms)::: aplicaciones que se ejecutan en Windows](images/allhanselman.png)

## <a name="platform-specifics"></a>Características específicas de las plataformas

Las características específicas de la plataforma permiten consumir funcionalidad que solo está disponible en una plataforma específica, sin necesidad de implementar representadores o efectos personalizados.

Se proporciona la siguiente funcionalidad específica de la plataforma para Xamarin.Forms vistas, páginas y diseños en el plataforma universal de Windows (UWP):

- Establecer una tecla de acceso para un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Para obtener más información, consulte [VisualElement Access Keys in Windows](visualelement-access-keys.md).
- Deshabilitar el modo de color heredado en un compatible [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Para obtener más información, consulte [modo de color heredado de VisualElement en Windows](legacy-color-mode.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las Xamarin.Forms vistas en UWP:

- Detectar el orden de lectura del contenido de texto en [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) las instancias de, y [`Label`](xref:Xamarin.Forms.Label) . Para obtener más información, vea [InputView el orden de lectura en Windows](inputview-reading-order.md).
- Habilitar la compatibilidad con gestos de TAP en un [`ListView`](xref:Xamarin.Forms.ListView) . Para obtener más información, vea [controles SelectionMode en Windows](listview-selectionmode.md).
- Habilitar la dirección de extracción de un `RefreshView` que se va a cambiar. Para obtener más información, consulte [dirección de extracción de RefreshView en Windows](refreshview-pulldirection.md).
- Habilitación de [`SearchBar`](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de revisión ortográfica. Para obtener más información, consulte [barra spell check en Windows](searchbar-spell-check.md).
- Establecer el subproceso en el que un [`WebView`](xref:Xamarin.Forms.WebView) hospeda su contenido. Para obtener más información, vea [modo de ejecución de WebView en Windows](webview-executionmode.md).
- Habilitación de [`WebView`](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje de UWP. Para obtener más información, vea [alertas de JavaScript de WebView en Windows](webview-javascript-alert.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para Xamarin.Forms las páginas en UWP:

- Contraer la [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) barra de navegación. Para obtener más información, consulte [barra de navegación FlyoutPage en Windows](flyoutpage-navigation-bar.md).
- Establecer opciones de colocación de la barra de herramientas. Para obtener más información, vea [Ubicación de la barra de herramientas de página en Windows](page-toolbar-placement.md).
- Habilitar iconos de página para mostrar en una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de herramientas. Para más información, consulte [Iconos de TabbedPage en Windows](tabbedpage-icons.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para la Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) clase en UWP:

- Especificar el directorio del proyecto desde el que se cargarán los recursos de imagen. Para obtener más información, consulte [Directorio de imagen predeterminado en Windows](default-image-directory.md).

## <a name="platform-support"></a>Compatibilidad con plataformas

Las Xamarin.Forms plantillas disponibles en Visual Studio contienen un proyecto plataforma universal de Windows (UWP).

> [!NOTE]
> Xamarin.Forms 1. x y 2. x admiten _Windows Phone 8 Silverlight_, _Windows Phone 8,1_ y _Windows 8.1_ desarrollo de aplicaciones. Sin embargo, estos tipos de proyecto están desusados.

## <a name="getting-started"></a>Introducción

Vaya a **archivo > nuevo proyecto de >** en Visual Studio y elija una de las plantillas **multiplataforma > aplicación en blanco ( Xamarin.Forms )** para comenzar.

Las Xamarin.Forms soluciones anteriores, o las creadas en MacOS, no tendrán todos los proyectos de Windows enumerados anteriormente (pero tienen que agregarse manualmente). Si la plataforma de Windows que desea destinar todavía no está en la solución, consulte las [instrucciones de configuración](installation/index.md) para agregar los tipos de proyecto de Windows deseados.

## <a name="samples"></a>Ejemplos

[Todos los ejemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) del libro de Charles Petzold que [*crean Mobile Apps Xamarin.Forms con*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) los proyectos de include plataforma universal de Windows (para Windows 10).

La [aplicación de demostración "Scott Hanselman"](https://github.com/jamesmontemagno/Hanselman.Forms) está disponible por separado y también incluye proyectos de Apple Watch y desgaste de Android (con Xamarin. iOS y Xamarin. Android, respectivamente, no se Xamarin.Forms ejecuta en esas plataformas).

## <a name="related-links"></a>Vínculos relacionados

- [Configurar proyectos de Windows](~/xamarin-forms/platform/windows/installation/index.md)
