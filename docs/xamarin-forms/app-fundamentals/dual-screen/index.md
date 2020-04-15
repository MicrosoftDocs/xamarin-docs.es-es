---
title: Doble pantalla de Xamarin.Forms
description: En esta guía se explica cómo crear aplicaciones de Xamarin.Forms para dispositivos de doble pantalla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 344b6293090ffa4281ea6351f7f176a5be37e5bd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "78165556"
---
# <a name="xamarinforms-dual-screen"></a>Doble pantalla de Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) y Surface Neo (Windows 10X) presentan nuevos patrones para aplicaciones táctiles. Xamarin.Forms incluye las clases `TwoPaneView` y `DualScreenInfo` para que pueda desarrollar aplicaciones para dispositivos de ese tipo.

## <a name="dual-screen-design-patterns"></a>[Modelos de diseño de doble pantalla](design-patterns.md)

Al considerar el uso óptimo de varias pantallas en un dispositivo de doble pantalla, consulte estas instrucciones de patrones para encontrar la mejor opción para la interfaz de la aplicación.

## <a name="dual-screen-layout"></a>[Diseño de doble pantalla](twopaneview.md)

La clase `TwoPaneView` de Xamarin.Forms, inspirada en el control UWP del mismo nombre, es un diseño multiplataforma optimizado para los dispositivos de pantalla doble.

## <a name="dual-screen-device-capabilities"></a>[Capacidades de dispositivos de doble pantalla](dual-screen-info.md)

La clase `DualScreenInfo` permite determinar en qué panel se encuentra la vista, qué tamaño tiene, en qué posición está el dispositivo, el ángulo de la bisagra y mucho más.

## <a name="dual-screen-platform-helpers"></a>[Asistentes de plataforma de doble pantalla](dual-screen-helper.md)

La clase `DualScreenHelper` permite comprobar si la plataforma admite la apertura de una nueva ventana como Imagen en imagen. En el caso de Neo, permite abrir una ventana que se mostrará en la barra táctil cuando el dispositivo esté en modo de redacción.

## <a name="dual-screen-triggers"></a>[Desencadenadores de doble pantalla](triggers.md)

El espacio de nombres de [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) incluye dos desencadenadores de estado que desencadenan un cambio de [`VisualState`](xref:Xamarin.Forms.VisualState) cuando se modifica el modo de vista del diseño adjunto o de la ventana.
