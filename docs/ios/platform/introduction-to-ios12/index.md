---
title: Introducción a iOS 12
description: En este documento se proporciona una descripción de alto nivel de algunas API de iOS 12 para las que la C# versión preliminar de Xamarin proporciona enlaces.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/08/2018
ms.openlocfilehash: 39d954626bc9e789446e7f1deac67e2e0fca51c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032003"
---
# <a name="introduction-to-ios-12"></a>Introducción a iOS 12

En este documento se proporciona una descripción de alto nivel de algunas API de iOS 12 para las que la C# versión preliminar de Xamarin proporciona enlaces.

Para empezar a compilar aplicaciones de iOS 12 con Xamarin, consulte la [Guía de introducción](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit es el marco de realidad aumentado incluido con iOS. ARKit 2 permite que varios usuarios interactúen entre sí en una escena de realidad aumentada, permite conservar los objetos en el espacio y volver a ellos más adelante, y proporciona reconocimiento de imágenes 2D y seguimiento de objetos 3D. iOS 12 también proporciona una apariencia rápida de AR, una manera de representar modelos de usdz AR en sus aplicaciones.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Métodos abreviados de Siri](siri-shortcuts.md)

Los métodos abreviados de Siri permiten a los desarrolladores integrar con mayor profundidad sus aplicaciones con Siri. Con los métodos abreviados de Siri, los usuarios pueden usar comandos de voz para abrir contenido o iniciar tareas en segundo plano, o pueden iniciar estas mismas tareas a través de accesos directos que Siri sugiere en la pantalla de bloqueo.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 reduce el tamaño de la aplicación mediante la cuantificación del modelo y los modelos flexibles, mejora el rendimiento de las aplicaciones con una nueva API de predicción por lotes y usa modelos personalizados para admitir los avances en el aprendizaje automático.

## <a name="notification-improvementsnotificationsindexmd"></a>[Mejoras en las notificaciones](notifications/index.md)

En iOS 12, las notificaciones agrupadas permiten presentar notificaciones de usuario en agrupaciones relacionadas con los subprocesos o aplicaciones. El texto de Resumen proporciona más información acerca de un grupo de notificación.

Las extensiones de contenido de notificación de iOS 12 permiten interfaces de usuario personalizadas y botones de acción dinámica.

## <a name="natural-language-frameworknatural-languagemd"></a>[Marco de lenguaje natural](natural-language.md)

El marco de lenguaje natural permite a las aplicaciones realizar varios tipos de análisis de lenguaje. Por ejemplo, puede identificar partes de la voz y determinar el idioma representado por un bloque de texto.

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[Vision Framework](~/ios/platform/introduction-to-ios11/vision.md)

Vision Framework incluye un detector de rostros mejorado que puede detectar rostros en diversas orientaciones. Además, las revisiones de solicitud pueden seleccionar la revisión específica del algoritmo de Vision Framework.

## <a name="photo-and-video-apis"></a>API de foto y vídeo

En iOS 12, la API de segmentación vertical devuelve un mate de efectos verticales: una máscara lineal que delimita el primer plano del fondo de una imagen vertical y es útil para crear varios efectos de imagen. iOS 12 también hace posible el uso de datos de profundidad de la cámara TrueDepth para efectos de vídeo en tiempo real.

## <a name="passwords"></a>Contraseñas

iOS 12 facilita a los usuarios y desarrolladores el trabajo con contraseñas:

- El Autorrelleno de contraseña y las contraseñas seguras automáticas permiten generar, almacenar y usar contraseñas seguras automáticamente en aplicaciones de iOS al registrarse e iniciar sesión en una aplicación.
- El código de seguridad Autorrellenar permite usar códigos de autenticación basados en SMS sin cortar ni pegar ni memorizar manualmente.
- La clase `ASWebAuthenticationSession` simplifica el proceso de trabajar con los servicios de autenticación federada.
- Autorrellenar las extensiones del proveedor de credenciales permiten a las aplicaciones de contraseña de terceros proporcionar el nombre de usuario y las contraseñas a los campos de inicio de sesión.

## <a name="healthkit-updates"></a>Actualizaciones de HealthKit

iOS 11,3 presentó [registros de mantenimiento](https://www.apple.com/healthcare/health-records/), lo que permite a los usuarios descargar su información de registro de estado de varias instituciones de atención sanitaria y verla en sus dispositivos iOS. iOS 12 agrega API que permiten a las aplicaciones de terceros acceder de forma segura a estos datos.

## <a name="imessage-app-presentation-contexts"></a>contextos de presentación de la aplicación iMessage

En iOS 12, las aplicaciones iMessage admiten contextos de presentación, que permiten que las aplicaciones se ejecuten como una aplicación iMessage normal o en el contexto de una foto o un efecto de vídeo.

## <a name="network-framework"></a>Marco de red

El marco de red, la pila de red que subyace a las API de `URLSession` que se usan habitualmente en las aplicaciones de iOS, ahora está disponible como un marco independiente, lo que facilita el trabajo con TCP, UDP, TLS, IPv4/IPv6, etc.

## <a name="carplay"></a>CarPlay

En iOS 12, las aplicaciones de terceros pueden ofrecer asignaciones y reactivar las instrucciones de navegación en CarPlay con el nuevo marco de CarPlay.

## <a name="deprecations"></a>Elementos obsoletos

Con iOS 12, Apple ha quedado en desuso:

- OpenGL ES, lo que [anima a los desarrolladores](https://developer.apple.com/ios/whats-new/) a adoptar metal.
- [`UIWebView`](xref:UIKit.UIWebView), [en favor de `WKWebView`](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).
