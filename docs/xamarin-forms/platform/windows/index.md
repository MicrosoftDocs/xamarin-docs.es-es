---
title: Características de la plataforma Windows
description: En este artículo se explica la compatibilidad con la plataforma Windows que está disponible en Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 694ec24697937b114036eceab1cafd5aae3617d8
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77635554"
---
# <a name="windows-platform-features"></a>Características de la plataforma Windows

El desarrollo de aplicaciones de Xamarin. Forms para plataformas Windows requiere Visual Studio. La [Página plataformas compatibles](~/get-started/supported-platforms.md) contiene más información sobre los requisitos previos.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Características específicas de las plataformas

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas, páginas y diseños de Xamarin. Forms en el Plataforma universal de Windows (UWP):

- Establecer una tecla de acceso para un [`VisualElement`](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement Access Keys in Windows](visualelement-access-keys.md).
- Deshabilitar el modo de color heredado en una [`VisualElement`](xref:Xamarin.Forms.VisualElement)compatible. Para obtener más información, consulte [modo de color heredado de VisualElement en Windows](legacy-color-mode.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin. Forms en UWP:

- Detectar el orden de lectura del contenido de texto en las instancias de [`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor)y [`Label`](xref:Xamarin.Forms.Label) . Para obtener más información, vea [InputView el orden de lectura en Windows](inputview-reading-order.md).
- Habilitar la compatibilidad con gestos de TAP en un [`ListView`](xref:Xamarin.Forms.ListView). Para obtener más información, vea [controles SelectionMode en Windows](listview-selectionmode.md).
- Habilitar la dirección de extracción de un `RefreshView` que se va a cambiar. Para obtener más información, consulte [dirección de extracción de RefreshView en Windows](refreshview-pulldirection.md).
- Habilitación de un [`SearchBar`](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de revisión ortográfica. Para obtener más información, consulte [barra spell check en Windows](searchbar-spell-check.md).
- Habilitación de un [`WebView`](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje de UWP. Para obtener más información, vea [alertas de JavaScript de WebView en Windows](webview-javascript-alert.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin. Forms en UWP:

- Contraer el [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) barra de navegación. Para obtener más información, consulte [barra de navegación MasterDetailPage en Windows](masterdetailpage-navigation-bar.md).
- Opciones de ubicación de la barra de herramientas de configuración. Para obtener más información, vea [Ubicación de la barra de herramientas de página en Windows](page-toolbar-placement.md).
- Habilitar iconos de página para mostrar en una barra de herramientas [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Para más información, consulte [Iconos de TabbedPage en Windows](tabbedpage-icons.md).

La siguiente funcionalidad específica de la plataforma se proporciona para la clase de [`Application`](xref:Xamarin.Forms.Application) de Xamarin. Forms en UWP:

- Especificar el directorio del proyecto desde el que se cargarán los recursos de imagen. Para obtener más información, consulte [Directorio de imagen predeterminado en Windows](default-image-directory.md).

## <a name="platform-support"></a>Compatibilidad con plataformas

Las plantillas de Xamarin. Forms disponibles en Visual Studio contienen un proyecto Plataforma universal de Windows (UWP).

> [!NOTE]
> Xamarin. Forms 1. x y 2. x admiten _Windows Phone 8 Silverlight_, _Windows Phone 8,1_y _Windows 8.1_ desarrollo de aplicaciones. Sin embargo, estos tipos de proyecto están desusados.

## <a name="getting-started"></a>Introducción

Vaya a **archivo > nuevo proyecto de >** en Visual Studio y elija una de las plantillas **multiplataforma > aplicación vacía (Xamarin. Forms)** para comenzar.

Las soluciones de Xamarin. Forms anteriores o las creadas en macOS no tendrán todos los proyectos de Windows enumerados anteriormente (pero deben agregarse manualmente). Si la plataforma de Windows que desea destinar todavía no está en la solución, consulte las [instrucciones de configuración](installation/index.md) para agregar los tipos de proyecto de Windows deseados.

## <a name="samples"></a>Ejemplos

[Todos los ejemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) del libro de Charles Petzold que [*crea Mobile Apps con Xamarin. forms*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluyen plataforma universal de Windows (para proyectos de Windows 10).

La [aplicación de demostración "Scott Hanselman"](https://github.com/jamesmontemagno/Hanselman.Forms) está disponible por separado y también incluye proyectos de Apple Watch y desgaste de Android (con Xamarin. iOS y Xamarin. Android, respectivamente, Xamarin. Forms no se ejecuta en esas plataformas).

## <a name="related-links"></a>Vínculos relacionados

- [Configurar proyectos de Windows](~/xamarin-forms/platform/windows/installation/index.md)
