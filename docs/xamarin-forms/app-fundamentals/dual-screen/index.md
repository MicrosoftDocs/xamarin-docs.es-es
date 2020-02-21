---
title: Doble pantalla de Xamarin.Forms
description: En esta guía se explica cómo crear aplicaciones de Xamarin.Forms para dispositivos de doble pantalla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 41a7ef4e447bb71582264b4e73629566d3ffd4e7
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480505"
---
# <a name="xamarinforms-dual-screen"></a>Doble pantalla de Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) y Surface Neo (Windows 10X) presentan nuevos patrones para aplicaciones táctiles. Xamarin.Forms incluye las clases `TwoPaneView` y `DualScreenInfo` para que pueda desarrollar aplicaciones para dispositivos de ese tipo.

## <a name="dual-screen-patterns"></a>[Patrones de doble pantalla](design-patterns.md)

Al considerar el uso óptimo de varias pantallas en un dispositivo de doble pantalla, consulte estas instrucciones de patrones para encontrar la mejor opción para la interfaz de la aplicación.

## <a name="twopaneview"></a>[TwoPaneView](twopaneview.md)

La clase `TwoPaneView` de Xamarin.Forms, inspirada en el control UWP del mismo nombre, es un diseño multiplataforma optimizado para los dispositivos de pantalla doble.

## <a name="dualscreeninfo"></a>[DualScreenInfo](dual-screen-info.md)

La clase `DualScreenInfo` permite determinar en qué panel se encuentra la vista, qué tamaño tiene, en qué posición está el dispositivo, el ángulo de la bisagra y mucho más.

## <a name="dualscreenhelper"></a>[DualScreenHelper](dual-screen-helper.md)

La clase `DualScreenHelper` permite comprobar si la plataforma admite la apertura de una nueva ventana como Imagen en imagen. En el caso de Neo, permite abrir una ventana que se mostrará en la barra táctil cuando el dispositivo esté en modo de redacción.
