---
title: controles de la interfaz de usuario de macOS en Xamarin. Mac
description: En este documento se proporcionan vínculos a guías que describen varios controles de interfaz de usuario disponibles para los desarrolladores de Xamarin. Mac. El contenido vinculado examina ventanas, cuadros de diálogo, alertas, menús, barras de herramientas, vistas de tabla, vistas de esquema, etc.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 7ae33883b83a4080903b524ea78e0da009ad0a3a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431523"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controles de la interfaz de usuario de macOS en Xamarin. Mac

_En este artículo se proporcionan vínculos a guías que describen varios controles de interfaz de usuario de macOS._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin. Mac, tiene acceso a los mismos controles de interfaz de usuario que un desarrollador que trabaja en *Objective-C* y *Xcode* . Como Xamarin. Mac se integra directamente con Xcode, puede usar los _Interface Builder_ de Xcode para crear y mantener las interfaces de usuario (o, opcionalmente, crearlas directamente en código de C#).

Las guías que se enumeran a continuación proporcionan información detallada sobre cómo trabajar con elementos de interfaz de usuario de macOS en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que usaremos en cada artículo.

Es posible que desee echar un vistazo a la sección [exponer clases y métodos de C# a Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) también en el documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , ya que se explican los `Register` `Export` atributos y que se usan para conectar las clases de C# a objetos de Objective-C y elementos de la interfaz de usuario.

## <a name="windows"></a>[Windows](~/mac/user-interface/window.md)

En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin. Mac. Aquí se explica cómo crear y mantener ventanas y paneles en Xcode y Interface Builder, cargar ventanas y paneles desde archivos. Storyboard o. Xib, usar Windows y responder a ventanas en código de C#.

## <a name="dialogs"></a>[Diálogos](~/mac/user-interface/dialog.md)

En este artículo se explica cómo trabajar con cuadros de diálogo y ventanas modales en una aplicación de Xamarin. Mac. Aquí se explica cómo crear y mantener ventanas modales en Xcode y Interface Builder, trabajar con cuadros de diálogo estándar y mostrar y responder a ventanas en código de C#.

## <a name="alerts"></a>[Alertas](~/mac/user-interface/alert.md)

En este artículo se explica cómo trabajar con alertas en una aplicación de Xamarin. Mac. Trata la creación y visualización de alertas del código de C# y la respuesta a las alertas.

## <a name="menus"></a>[Menús](~/mac/user-interface/menu.md)

Los menús se usan en varias partes de la interfaz de usuario de una aplicación Mac. desde el menú principal de la aplicación en la parte superior de la pantalla hasta menús emergentes y menús contextuales que pueden aparecer en cualquier parte de una ventana. Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac. En este artículo se explica cómo trabajar con menús de coco en una aplicación de Xamarin. Mac.

## <a name="standard-controls"></a>[Controles estándar](~/mac/user-interface/standard-controls.md)

Trabajar con los controles estándar de AppKit, como botones, etiquetas, campos de texto, casillas y controles segmentados en una aplicación de Xamarin. Mac. En esta guía se explica cómo agregarlos a un diseño de la interfaz de usuario en la Interface Builder de Xcode, exponerlos al código mediante salidas y acciones, y trabajar con controles AppKit en el código de C#.

## <a name="toolbars"></a>[Barras de herramientas](~/mac/user-interface/toolbar.md)

En este artículo se explica cómo trabajar con barras de herramientas en una aplicación de Xamarin. Mac. Se trata la creación y el mantenimiento de barras de herramientas en Xcode y Interface Builder, cómo exponer los elementos de la barra de herramientas al código mediante salidas y acciones, habilitar y deshabilitar elementos de la barra de herramientas y finalmente responder a los elementos de la barra de herramientas en código de C#.

## <a name="table-views"></a>[Vistas de tabla](~/mac/user-interface/table-view.md)

En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de tabla en Xcode y Interface Builder, cómo exponer los elementos de la vista de tabla al código mediante salidas y acciones, rellenar las vistas de tabla y responder a los elementos de la vista de tabla en código de C#.

## <a name="outline-views"></a>[Vistas de esquema](~/mac/user-interface/outline-view.md)

En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de esquema en Xcode y Interface Builder, cómo exponer los elementos de la vista de esquema al código mediante salidas y acciones, rellenar las vistas de esquema y responder a los elementos de la vista de esquema en el código de C#.

## <a name="source-lists"></a>[Listas de origen](~/mac/user-interface/source-list.md)

En este artículo se explica cómo trabajar con listas de origen en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener listas de origen en Xcode y Interface Builder, cómo exponer elementos de la lista de origen al código mediante salidas y acciones, rellenar listas de origen y responder a los elementos de la lista de origen en código de C#.

## <a name="collection-views"></a>[Vistas de colección](~/mac/user-interface/collection-view.md)

En este artículo se explica cómo trabajar con vistas de colección en una aplicación de Xamarin. Mac. En él se explica cómo crear y mantener vistas de colección en Xcode y Interface Builder, cómo exponer los elementos de la vista de colección al código mediante salidas y acciones, rellenar las vistas de colección y responder a las vistas de colección en el código de C#.

## <a name="creating-custom-controls"></a>[Crear controles personalizados](~/mac/user-interface/custom-controls.md)

En este artículo se explica cómo crear controles de interfaz de usuario personalizados (heredando de `NSControl` ), dibujar una interfaz personalizada para el control y crear acciones personalizadas que se pueden usar con la Interface Builder de Xcode.

## <a name="mac-samples-gallery"></a>Galería de muestras de Mac

También se recomienda echar un vistazo a la [Galería de ejemplos de Mac](/samples/browse/?products=xamarin&term=Xamarin.Mac). Incluye una gran cantidad de código listo para usar que puede ayudarle a obtener rápidamente un proyecto de Xamarin. Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)