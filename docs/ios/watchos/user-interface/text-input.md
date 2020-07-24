---
title: Trabajar con la entrada de texto de watchos en Xamarin
description: En este documento se describe la entrada de texto de watchos en Xamarin. Describe el método PresentTextInputController, scribbling, texto sin formato, emojis y dictado.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: f523b6a028c8d9dcc0df772dc617c57bc947905d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936894"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Trabajar con la entrada de texto de watchos en Xamarin

El Apple Watch no proporciona un teclado para que los usuarios escriban texto, pero admite algunas alternativas fáciles de ver:

- Seleccionar en una lista predefinida de opciones de texto,
- Dictado Siri,
- Elección de un Emoji,
- Reconocimiento de escritura a mano de la letra de garabatear (introducido en watchos 3).

El simulador no admite actualmente el dictado, pero todavía puede probar otras opciones del controlador de entrada de texto, como Scribble, como se muestra aquí:

![Probar la opción Scribble](text-input-images/textinput-sml.png)

Para aceptar la entrada de texto en una aplicación de inspección:

1. Cree una matriz de cadenas de opciones predefinidas.
2. Llame a `PresentTextInputController` con la matriz, ya sea para permitir Emoji o not, y un `Action` que se llame cuando el usuario haya finalizado.
3. En la acción de finalización, pruebe el resultado de la entrada y tome las medidas adecuadas en la aplicación (posiblemente estableciendo el valor de texto de una etiqueta).

El siguiente fragmento de código presenta tres opciones predefinidas para el usuario:

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

La `WKTextInputMode` enumeración tiene tres valores:

- Texto
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Texto

Cuando se establece el modo sin formato, el usuario puede elegir:

- Dictado
- Scribble o
- a partir de una lista predefinida que proporciona la aplicación.

[![Dictado, Scribble o a partir de una lista predefinida que la aplicación proporciona](text-input-images/plain-scribble-sml.png)](text-input-images/plain-scribble.png#lightbox)

El resultado siempre se devuelve como un `NSObject` que se puede convertir en `string` .

## <a name="emoji"></a>Emoji

Hay dos tipos de Emoji:

- Emoji normal de Unicode
- Imágenes animadas

Cuando el usuario elige un Emoji de Unicode, se devuelve como una cadena.

Si se selecciona un Emoji de imagen animado `result` , en el controlador de finalización contendrá un `NSData` objeto que contiene el Emoji `UIImage` .

## <a name="accepting-dictation-only"></a>Aceptación solo del dictado

Para llevar al usuario directamente a la pantalla de dictado sin mostrar ninguna sugerencia (o la opción Scribble):

- Pase una matriz vacía para la lista de sugerencias y
- establecer `WatchKit.WKTextInputMode.Plain` .

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

Cuando el usuario está hablando, la pantalla inspección muestra la pantalla siguiente, que incluye el texto tal como se entiende (por ejemplo, "Esto es una prueba"):

![Cuando el usuario está hablando, la pantalla inspección muestra el texto tal y como se entiende](text-input-images/dictation.png)

Una vez que presione el botón **Done** , se devolverá el texto.

## <a name="related-links"></a>Vínculos relacionados

- [Documento de texto y etiquetas de Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introducción a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
