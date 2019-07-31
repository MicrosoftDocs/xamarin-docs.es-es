---
title: controles de la interfaz de usuario de macOS en Xamarin. Mac
description: En este documento se proporcionan vínculos a guías que describen varios controles de interfaz de usuario disponibles para los desarrolladores de Xamarin. Mac. El contenido vinculado examina ventanas, cuadros de diálogo, alertas, menús, barras de herramientas, vistas de tabla, vistas de esquema, etc.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: 7dfd8392abf832e53cb90d0a09990fc5ef57b0bb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656321"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controles de la interfaz de usuario de macOS en Xamarin. Mac

_En este artículo se proporcionan vínculos a guías que describen varios controles de interfaz de usuario de macOS._

Al trabajar con C# y .net en una aplicación de Xamarin. Mac, tiene acceso a los mismos controles de interfaz de usuario que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las interfaces de usuario (o, opcionalmente, C# crearlas directamente en el código).

Las guías que se enumeran a continuación proporcionan información detallada sobre cómo trabajar con elementos de interfaz de usuario de macOS en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en todos los artículos.

Es posible que desee echar un vistazo a la [sección exponer C# clases y métodos a Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , como se explica en los `Register` atributos y `Export` que se usan para conectar las C# clases. a objetos de Objective-C y elementos de la interfaz de usuario.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin. Mac. Aquí se explica cómo crear y mantener ventanas y paneles en Xcode y Interface Builder, cargar ventanas y paneles desde archivos. Storyboard o. Xib, usar Windows y responder a ventanas C# en código.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Cuadros de diálogo](~/mac/user-interface/dialog.md)

En este artículo se explica cómo trabajar con cuadros de diálogo y ventanas modales en una aplicación de Xamarin. Mac. Aquí se explica cómo crear y mantener ventanas modales en Xcode y Interface Builder, trabajar con cuadros de diálogo estándar y mostrar y responder C# a ventanas en código.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

En este artículo se explica cómo trabajar con alertas en una aplicación de Xamarin. Mac. Trata la creación y visualización de alertas C# desde el código y la respuesta a las alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menús](~/mac/user-interface/menu.md)

Los menús se usan en varias partes de la interfaz de usuario de una aplicación Mac. desde el menú principal de la aplicación en la parte superior de la pantalla hasta menús emergentes y menús contextuales que pueden aparecer en cualquier parte de una ventana. Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac. En este artículo se explica cómo trabajar con menús de coco en una aplicación de Xamarin. Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles estándar](~/mac/user-interface/standard-controls.md)

Trabajar con los controles estándar de AppKit, como botones, etiquetas, campos de texto, casillas y controles segmentados en una aplicación de Xamarin. Mac. En esta guía se explica cómo agregarlos a un diseño de interfaz de usuario en la Interface Builder de Xcode, exponerlos al código mediante salidas y acciones, y C# trabajar con controles AppKit en el código.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de herramientas](~/mac/user-interface/toolbar.md)

En este artículo se explica cómo trabajar con barras de herramientas en una aplicación de Xamarin. Mac. Se trata la creación y el mantenimiento de barras de herramientas en Xcode y Interface Builder, cómo exponer los elementos de la barra de herramientas al código mediante salidas y acciones, cómo habilitar y deshabilitar elementos de la C# barra de herramientas y finalmente responder a los elementos de la barra de herramientas en el código.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Vistas de tabla](~/mac/user-interface/table-view.md)

En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de tabla en Xcode y Interface Builder, cómo exponer los elementos de la vista de tabla al código mediante salidas y acciones, rellenar las vistas de C# tabla y responder a los elementos de la vista de tabla en el código.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Vistas de esquema](~/mac/user-interface/outline-view.md)

En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de esquema en Xcode y Interface Builder, cómo exponer los elementos de la vista de esquema al código mediante salidas y acciones, rellenar las vistas de C# esquema y responder a los elementos de la vista de esquema en el código.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listas de origen](~/mac/user-interface/source-list.md)

En este artículo se explica cómo trabajar con listas de origen en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener listas de origen en Xcode y Interface Builder, cómo exponer elementos de la lista de origen al código mediante salidas y acciones, rellenar listas de origen C# y responder a los elementos de la lista de origen en el código.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Vistas de colección](~/mac/user-interface/collection-view.md)

En este artículo se explica cómo trabajar con vistas de colección en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de colección en Xcode y Interface Builder, cómo exponer los elementos de la vista de colección al código mediante salidas y acciones, rellenar las vistas C# de colección y responder a las vistas de colección en el código.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Crear controles personalizados](~/mac/user-interface/custom-controls.md)

En este artículo se explica cómo crear controles de interfaz de usuario personalizados `NSControl`(heredando de), dibujar una interfaz personalizada para el control y crear acciones personalizadas que se pueden usar con la Interface Builder de Xcode.

## <a name="mac-samples-gallery"></a>Galería de muestras de Mac

También se recomienda echar un vistazo a la [Galería de ejemplos de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac). Incluye una gran cantidad de código listo para usar que puede ayudarle a obtener rápidamente un proyecto de Xamarin. Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
