---
title: Introducción a macOS Mojave
description: En este documento se proporciona información general de alto nivel de las características nuevas y actualizadas de macOS Mojave.
ms.prod: xamarin
ms.assetid: 4A41CD85-C807-44C9-85AB-B5441B145A73
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: dc3def5d5c709515574a72cf9f61d71d67690d87
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429700"
---
# <a name="introduction-to-macos-mojave"></a>Introducción a macOS Mojave

En este documento se proporciona información general de alto nivel de las características nuevas y actualizadas de macOS Mojave.

Para empezar a compilar aplicaciones de macOS Mojave con Xamarin, eche un vistazo a la [Guía de introducción](~/mac/platform/introduction-to-macos-mojave/get-started.md) para [xamarin. Mac 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/mac/xamarin.mac_5/xamarin.mac_5.0.md).

## <a name="dark-mode"></a>Modo oscuro

El modo oscuro es un tema oscuro en todo el sistema en macOS Mojave que usa una combinación de colores gris oscuro y dinámico para mostrar los elementos de la interfaz de usuario. También presenta nuevos colores de énfasis, efectos de color y colores de matiz de contenido para ayudar a las aplicaciones de terceros a tener un aspecto correcto independientemente de la configuración de color del usuario.

## <a name="user-notifications-framework"></a>Marco de notificaciones de usuario

El marco de trabajo de notificaciones de usuario se incluye en macOS Mojave y cambia las API que usan las aplicaciones Mac para trabajar con las notificaciones de usuario.

## <a name="natural-language-framework"></a>Marco de lenguaje natural

El marco de lenguaje natural permite a las aplicaciones realizar varios tipos de análisis de lenguaje. Por ejemplo, se puede usar para identificar partes de la voz y determinar el idioma representado por un bloque de texto.

## <a name="vision-framework"></a>Vision Framework

Vision Framework incluye un detector de rostros mejorado que puede detectar rostros en diversas orientaciones. Además, ahora se pueden usar las revisiones de solicitud para seleccionar una revisión específica del algoritmo de Vision Framework.

## <a name="network-framework"></a>Marco de red

El marco de red, la pila de red que subyace `URLSession` a las API que se usan habitualmente en las aplicaciones de iOS, ahora está disponible como un marco independiente, lo que facilita el trabajo con TCP, UDP, TLS, IPv4/IPv6, etc.

## <a name="deprecations"></a>Elementos obsoletos

Con macOS Mojave, Apple ha dejado de usar OpenGL ES y OpenCL, lo que [anima a los desarrolladores](https://developer.apple.com/macos/whats-new/) a adoptar sombreadores de rendimiento de metal y metal.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Xamarin.Mac](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [macOS – desarrollador de Apple](https://developer.apple.com/macos/)
- [Notas de la versión de Xamarin. Mac 5,0](/xamarin/mac/release-notes/5/5.0/)