---
title: Gestos de Xamarin.Forms
description: En esta guía se explica cómo se pueden usar reconocedores de gestos de Xamarin.Forms para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1e93f74ab8ef6d63213a8fbdc7ec45a794cf55
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137882"
---
# <a name="xamarinforms-gestures"></a>Gestos de Xamarin.Forms

_Se pueden usar reconocedores de gestos para detectar la interacción del usuario con las vistas en una aplicación de Xamarin.Forms._

La clase [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) de Xamarin.Forms admite los gestos de pulsar, reducir, desplazar lateralmente y deslizar rápidamente en instancias de [`View`](xref:Xamarin.Forms.View).

## <a name="adding-a-tap-gesture-recognizer"></a>[Agregar un reconocedor de gesto de pulsar](tap.md)

Se usa un gesto de pulsar para la detección de pulsación y se reconoce con la clase [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).

## <a name="adding-a-pinch-gesture-recognizer"></a>[Agregar un reconocedor de gesto de reducir](pinch.md)

Se usa un gesto de reducir para realizar un zoom interactivo y se reconoce con la clase [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer).

## <a name="adding-a-pan-gesture-recognizer"></a>[Agregar un reconocedor de gesto de desplazar lateralmente](pan.md)

Se usa un gesto de desplazar lateralmente para detectar el movimiento de los dedos alrededor de la pantalla y aplicar ese movimiento al contenido; se reconoce con la clase [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer).

## <a name="adding-a-swipe-gesture-recognizer"></a>[Agregar un reconocedor de gesto de deslizar rápidamente](swipe.md)

Un gesto de deslizar rápidamente se produce cuando un dedo se mueve a través de la pantalla en dirección horizontal o vertical y, a menudo, se usa para iniciar la navegación a través del contenido. Los gestos de deslizar rápidamente se reconocen con la clase [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer).
