---
title: Gestos de Xamarin.Forms
description: En esta guía se explica cómo se pueden usar reconocedores de gestos de Xamarin.Forms para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7528afd0971cf06eb69df4ed7c08c3fd6dcc9e22
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917826"
---
# <a name="no-locxamarinforms-gestures"></a>Gestos de Xamarin.Forms

_Se pueden usar reconocedores de gestos para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms._

La clase [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) de Xamarin.Forms admite los gestos de pulsar, reducir, desplazar lateralmente, deslizar rápidamente y arrastrar y colocar en instancias de [`View`](xref:Xamarin.Forms.View).

## <a name="add-a-tap-gesture-recognizer"></a>[Incorporación de un reconocedor de gesto de pulsar](tap.md)

Se usa un gesto de pulsar para la detección de pulsación y se reconoce con la clase [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).

## <a name="add-a-pinch-gesture-recognizer"></a>[Incorporación de un reconocedor de gesto de reducir](pinch.md)

Se usa un gesto de reducir para realizar un zoom interactivo y se reconoce con la clase [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer).

## <a name="add-a-pan-gesture-recognizer"></a>[Incorporación de un reconocedor de gesto de desplazamiento lateral](pan.md)

Se usa un gesto de desplazar lateralmente para detectar el movimiento de los dedos alrededor de la pantalla y aplicar ese movimiento al contenido; se reconoce con la clase [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer).

## <a name="add-a-swipe-gesture-recognizer"></a>[Incorporación de un reconocedor de gesto de deslizar rápidamente](swipe.md)

Un gesto de deslizar rápidamente se produce cuando un dedo se mueve a través de la pantalla en dirección horizontal o vertical y, a menudo, se usa para iniciar la navegación a través del contenido. Los gestos de deslizar rápidamente se reconocen con la clase [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer).

## <a name="add-a-drag-and-drop-gesture-recognizer"></a>[Incorporación de un reconocedor de gesto de arrastrar y colocar](drag-and-drop.md)

Un gesto de arrastrar y colocar permite arrastrar elementos y sus paquetes de datos asociados desde una ubicación en pantalla a otra ubicación mediante un gesto continuo. Los gestos de arrastre y colocación se reconocen con las clases `DragGestureRecognizer` y `DropGestureRecognizer`, respectivamente.
