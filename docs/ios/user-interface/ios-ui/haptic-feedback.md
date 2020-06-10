---
title: Proporcionar comentarios hápticos en Xamarin. iOS
description: En este documento se describe cómo proporcionar comentarios hápticos en una aplicación Xamarin. iOS. Describe UIImpactFeedbackGenerator, UINotificationFeedbackGenerator y UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 86c7c726465257d71b6b407686153650dddbcbca
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574475"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Proporcionar comentarios hápticos en Xamarin. iOS

<a name="Overview"></a>

## <a name="overview"></a>Información general

En iPhone 7 y iPhone 7 Plus, Apple ha incluido nuevas respuestas hápticos que proporcionan maneras adicionales de atraer físicamente al usuario. Los comentarios hápticos (a menudo denominados simplemente los hápticos) usan el sentido de tocar (a través de fuerza, vibraciones o movimientos) en el diseño de la interfaz de usuario. Use estas nuevas opciones de comentarios táctiles para obtener la atención del usuario y reforzar sus acciones.

Los siguientes temas se tratarán en detalle:

- [Acerca de los comentarios hápticos](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback"></a>

## <a name="about-haptic-feedback"></a>Acerca de los comentarios hápticos

Varios elementos integrados de la interfaz de usuario ya proporcionan comentarios hápticos, como los selectores, los conmutadores y los controles deslizantes. iOS 10 ahora agrega la capacidad de desencadenar los hápticos mediante programación con una subclase concreta de la `UIFeedbackGenerator` clase.

El desarrollador puede usar una de las siguientes `UIFeedbackGenerator` subclases para desencadenar comentarios hápticos mediante programación:

- `UIImpactFeedbackGenerator`-Use este generador de comentarios para complementar una acción o tarea, como presentar un "thud" cuando una vista se desliza hacia el lugar o si dos objetos en pantalla entran en conflicto.
- `UINotificationFeedbackGenerator`-Use este generador de comentarios para las notificaciones, como una acción completada, con errores o cualquier otro tipo de advertencia.
- `UISelectionFeedbackGenerator`-Use este generador de comentarios para una selección que cambie activamente, como la selección de un elemento de una lista.

<a name="UIImpactFeedbackGenerator"></a>

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Use este generador de comentarios para complementar una acción o tarea, como presentar un "thud" cuando una vista se desliza hacia el lugar o si dos objetos en pantalla entran en conflicto.

Use el código siguiente para desencadenar los comentarios de impacto:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Cuando el desarrollador crea una instancia nueva de la `UIImpactFeedbackGenerator` clase, proporciona un que `UIImpactFeedbackStyle` especifica la seguridad de los comentarios como:

- `Heavy`
- `Medium`
- `Light`

`Prepare`Se llama al método de `UIImpactFeedbackGenerator` para informar al sistema de que los comentarios hápticos están a punto de producirse para que pueda minimizar la latencia.

`ImpactOccurred`Después, el método desencadena comentarios hápticos.

<a name="UINotificationFeedbackGenerator"></a>

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Use este generador de comentarios para las notificaciones, como una acción completada, con errores o cualquier otro tipo de advertencia.

Use el código siguiente para desencadenar comentarios sobre las notificaciones:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Se crea una nueva instancia de la `UINotificationFeedbackGenerator` clase y `Prepare` se llama a su método para informar al sistema de que los comentarios hápticos están a punto de producirse para que pueda minimizar la latencia.

`NotificationOccurred`Se llama a para desencadenar comentarios hápticos con un determinado `UINotificationFeedbackType` de:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator"></a>

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Use este generador de comentarios para una selección que cambie activamente, como la selección de un elemento de una lista.

Use el código siguiente para desencadenar los comentarios de la selección:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Se crea una nueva instancia de la `UISelectionFeedbackGenerator` clase y `Prepare` se llama a su método para informar al sistema de que los comentarios hápticos están a punto de producirse para que pueda minimizar la latencia.

`SelectionChanged`Después, el método desencadena comentarios hápticos.

## <a name="summary"></a>Resumen

En este artículo se han tratado los nuevos tipos de comentarios hápticos disponibles en iOS 10 y cómo implementarlos en Xamarin. iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
