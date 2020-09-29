---
title: Controles de la interfaz de usuario de watchos en Xamarin
description: En este documento se describen los distintos controles que están disponibles para su uso en las interfaces de usuario de watchos. Proporciona una descripción de etiquetas, botones, conmutadores, controles deslizantes, imágenes, separadores, mapas, etc.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: 3d47b8d524d8f798ab9c0a654fde8246b9fbcc36
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437159"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Controles de la interfaz de usuario de watchos en Xamarin

En el ejemplo [**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) se muestran varios controles watchos. Aquí se muestra el guión gráfico de la aplicación (haga clic para hacer zoom):

[![Diseño de ejemplo de watchos](images/storyboard-sml.png)](images/storyboard.png#lightbox)

Los nombres de programación de todos los controles tienen el prefijo `WKInterface` (por ejemplo, `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Descripción|Instantánea|
|---|---|---|
|Etiqueta|Use `SetText` y otras propiedades para controlar la apariencia del texto en un control de etiqueta. `NSAttributedString` también se admite.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![Captura de pantalla de etiqueta](Images/label.png)|
|Botón|Crear y establecer propiedades en el guión gráfico. Ctrl + arrastre para agregar un `Action` para implementar un controlador para cuando se haga clic en él.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![Captura de pantalla del botón](Images/button.png)|
|Conmutador|`SetOn`Se usa para controlar el estado del conmutador.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![Captura de pantalla del conmutador](Images/switch.png)|
|Control deslizante|Son posibles muchos estilos diferentes.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![Captura de pantalla deslizante](Images/slider.png)|
|Imagen|Use `myImage.SetImage("MyWatchImage")` para cargar imágenes en el reloj o `WKInterfaceDevice.CurrentDevice.AddCachedImage` para almacenarlas en caché para su uso repetido en el reloj.<br />[Documentación de control de imagen](~/ios/watchos/user-interface/image.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![Captura de pantalla de imagen](Images/image.png)|
|Separador|Use separadores para ayudar a crear interfaces de aplicaciones de inspección atractivas.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![Captura de pantalla del separador](Images/separator.png)|
|Map|La imagen del mapa se muestra estáticamente en el reloj, pero puede controlar muchos aspectos de su apariencia, como agregar PIN.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![Captura de pantalla de mapa](Images/map.png)|
|Película & InlineMove|Las películas pueden abrirse por sí solos o en línea<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![Captura de pantalla de película](Images/movie.png)|
|Group (Grupo)|Use grupos para ayudar a crear interfaces de aplicaciones de inspección atractivas.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![Captura de pantalla del grupo](Images/group.png)|
|Tabla|Una versión simplificada de las tablas en iOS. Implemente `DidSelectRow` para responder a la selección del usuario (o use un segue).<br />[Documentación del control de tabla](~/ios/watchos/user-interface/table.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![Captura de pantalla de tabla](Images/table.png)|
|Dispositivo|`WKInterfaceDevice.CurrentDevice` incluye propiedades como `ScreenBounds` , `ScreenScale` y `PreferredContentSizeCategory` .<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![Captura de pantalla del dispositivo](Images/device.png)|
|[Menú](~/ios/watchos/user-interface/menu.md)|Defina el menú de pulsación forzada en el guión gráfico e implemente las acciones para cada botón del código.<br />[Documentación de control de menú (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![Captura de pantalla del menú](Images/controller.png)|
|Entrada de texto|Use `PresentTextInputController` y la `WKTextInputMode` enumeración.<br />[Documentación de entrada de texto](~/ios/watchos/user-interface/text-input.md)<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![Captura de pantalla de entrada de texto](Images/textinput.png)|
|Digital Crown|El Digital Crown se puede usar para controlar un selector o se puede realizar un seguimiento de la rotación en el código.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![Captura de pantalla de la corona digital](Images/digital-crown.png)|
|Gestos|Hay cuatro tipos de reconocimiento de gestos que se pueden agregar a una escena: Pulse, deslice el dedo, pan y LongPress.<br />[Código de catálogo](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![Captura de pantalla de gestos](Images/gestures.png)|

## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Referencia de la API del kit de inspección](xref:WatchKit)