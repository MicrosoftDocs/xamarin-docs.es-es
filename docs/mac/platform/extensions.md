---
title: Compatibilidad con las extensiones de Xamarin.Mac
description: En este documento se describe la compatibilidad de Xamarin. Mac con las extensiones Finder, Share y hoy. Examina las limitaciones y los problemas conocidos, los vínculos a un tutorial y una aplicación de ejemplo, y proporciona sugerencias para trabajar con extensiones.
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 58250770b925c20b912b71e5591b1d0e252c801a
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119035"
---
# <a name="xamarinmac-extension-support"></a>Compatibilidad con las extensiones de Xamarin.Mac

En Xamarin. Mac 2,10 se agregó compatibilidad con varios puntos de extensión macOS:

- Selector
- Compartir
- Hoy

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

A continuación se indican las limitaciones y los problemas conocidos que pueden producirse al desarrollar extensiones en Xamarin. Mac:

- Actualmente no hay compatibilidad con la depuración en Visual Studio para Mac. Toda la depuración tendrá que realizarse a través de **NSLog** y la **consola**de. Vea la sección sugerencias a continuación para obtener más información.
- Las extensiones deben estar contenidas en una aplicación host, que cuando se ejecuta una vez con el registro en el sistema. A continuación, se deben habilitar en la sección de **extensión** de **preferencias del sistema**. 
- Algunos bloqueos de extensión pueden desestabilizar la aplicación host y causar un comportamiento extraño. En concreto, el **buscador** y la sección **hoy** del **Centro** de notificaciones pueden quedar "atascados" y dejar de responder. Esto también se ha experimentado en los proyectos de extensión en Xcode y actualmente no está relacionado con Xamarin. Mac. A menudo, esto se puede ver en el registro del sistema (a través de la **consola**, consulte sugerencias para obtener detalles) imprimir mensajes de error repetidos. Parece que se está reiniciando macOS para corregirlo.

<a name="Tips" />

## <a name="tips"></a>Sugerencias

Las siguientes sugerencias pueden ser útiles al trabajar con extensiones en Xamarin. Mac:

- Como Xamarin. Mac actualmente no admite extensiones de depuración, la experiencia de depuración dependerá principalmente `printf` de la ejecución y de las instrucciones. Sin embargo, las extensiones se ejecutan en un `Console.WriteLine` proceso de espacio aislado, por lo que no actuará como lo hace en otras aplicaciones de Xamarin. Mac. Al invocar [ `NSLog` directamente](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554) , se generarán mensajes de depuración en el registro del sistema.
- Cualquier excepción no detectada bloqueará el proceso de extensión, lo que proporciona solo una pequeña cantidad de información útil en el **registro del sistema**. Puede ser útil ajustar el `try/catch` código problemático en un `NSLog`bloque (excepción) antes de volver a producirlo.
- Se puede tener acceso al **registro del sistema** desde la aplicación de **consola** en **aplicaciones** > **utilidades**:

    [![](extensions-images/extension02.png "Registro del sistema")](extensions-images/extension02.png#lightbox)
- Como se indicó anteriormente, la ejecución de la aplicación host de extensión la registrará en el sistema. Eliminando la agrupación de aplicaciones con el anulación del registro. 
- Si se registran las versiones "apartadas" de las extensiones de una aplicación, use el siguiente comando para localizarlas (para que se puedan eliminar):`plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>Tutorial y aplicación de ejemplo

Dado que el desarrollador creará y trabajará con las extensiones de Xamarin. Mac de la misma manera que las extensiones de Xamarin. iOS, consulte la documentación [Introducción a las extensiones](~/ios/platform/extensions.md) para obtener más detalles.

[Aquí](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)puede encontrar un proyecto de Xamarin. Mac de ejemplo que contiene pequeños ejemplos funcionales de cada tipo de extensión.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha examinado rápidamente el trabajo con extensiones en una aplicación de Xamarin. Mac versión 2,10 (y posteriores).

## <a name="related-links"></a>Vínculos relacionados

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://docs.microsoft.com/samples/xamarin/mac-samples/extensionsamples)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
