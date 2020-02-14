---
title: Características de doble pantalla de Xamarin.Forms
description: En esta guía se explica cómo Xamarin.Forms puede optimizar con facilidad las aplicaciones para dispositivos de doble pantalla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1f648297a608c592d90f2c70494ae4496fe73c0f
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145519"
---
# <a name="xamarinforms-dual-screen"></a>Doble pantalla de Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) y Surface Neo (Windows 10X) presentan nuevos patrones para aplicaciones táctiles. Xamarin.Forms presenta `TwoPaneView` y `DualScreenInfo` para que pueda aprovechar al máximo estas nuevas experiencias.

## <a name="dual-screen-patternsdesign-patternsmd"></a>[Patrones de doble pantalla](design-patterns.md)

Al considerar el uso óptimo de varias pantallas en un dispositivo de doble pantalla, consulte nuestras instrucciones de patrones para encontrar la mejor opción para la interfaz de la aplicación.

## <a name="twopaneviewtwopaneviewmd"></a>[TwoPaneView](twopaneview.md)

La clase `TwoPaneView` de Xamarin.Forms, inspirada por el control UWP del mismo nombre, presenta un diseño multiplataforma optimizado para los dispositivos de pantalla doble.

## <a name="dualscreeninfodual-screen-infomd"></a>[DualScreenInfo](dual-screen-info.md)

Para sacar el máximo partido de los dispositivos de pantalla doble, es posible que quiera saber en qué panel se encuentra la vista, qué tamaño tiene, en qué posición está el dispositivo, el ángulo de la bisagra y mucho más. `DualScreenInfo` proporciona esta información.

## <a name="dualscreenhelperdual-screen-helpermd"></a>[DualScreenHelper](dual-screen-helper.md)
`DualScreenHelper` permite comprobar si la plataforma admite la apertura de una nueva ventana como Imagen en imagen. En el caso de Neo, permite abrir una ventana que se mostrará en la barra táctil cuando el dispositivo esté en modo de redacción.