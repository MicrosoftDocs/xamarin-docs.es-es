---
title: Referencia del controlador para juegos de monojuego
description: En este documento se describe el controlador de juegos, una clase multiplataforma para acceder a dispositivos de entrada en monogame. Describe cómo leer la entrada del controlador de juegos y proporciona código de ejemplo.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 11b1cfda435e97b09f9d1eded22f11f912d1783d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934035"
---
# <a name="monogame-gamepad-reference"></a>Referencia del controlador para juegos de monojuego

_El controlador de juegos es una clase estándar multiplataforma para acceder a dispositivos de entrada en monogame._

`GamePad`se puede usar para leer la entrada de dispositivos de entrada en varias plataformas monogame. En esta guía se muestra cómo trabajar con la clase de controlador de juegos. Puesto que cada dispositivo de entrada difiere en el diseño y el número de botones que proporciona, esta guía incluye diagramas que muestran las diversas asignaciones de dispositivos.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>Controlador para juegos como reemplazo de Xbox360GamePad

La API XNA original ofrecía la `Xbox360GamePad` clase para leer la entrada de un dispositivo de juego en la consola Xbox 360 o PC. Monogame lo ha reemplazado por una `GamePad` clase, ya que los controladores de Xbox 360 no se pueden usar en la mayoría de las plataformas monogame (como iOS o Xbox One). A pesar del cambio de nombre, el uso de la `GamePad` clase es similar a la `Xbox360GamePad` clase.

## <a name="reading-input-from-gamepad"></a>Lectura de la entrada desde el controlador de juegos

La `GamePad` clase proporciona una forma estandarizada de leer la entrada en cualquier plataforma monogame. Proporciona información a través de dos métodos:

- `GetState`: devuelve el estado actual de los botones del controlador, los palos analógicos y el panel d.
- `GetCapabilities`: devuelve información acerca de las capacidades de hardware, por ejemplo, si el controlador tiene determinados botones o admite vibraciones.

### <a name="example-moving-a-character"></a>Ejemplo: mover un carácter

En el código siguiente se muestra cómo se puede usar el control Thumb izquierdo para desplace un carácter mediante el establecimiento de sus `XVelocity` `YVelocity` propiedades y. En este código se supone que `characterInstance` es una instancia de un objeto que tiene `XVelocity` `YVelocity` las propiedades y:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Ejemplo: detectar inserciones

`GamePadState`proporciona información sobre el estado actual del controlador, por ejemplo, si se presiona un botón determinado. Ciertas acciones, como realizar un salto de carácter, requieren comprobar si el botón se insertó (no se deshizo el último fotograma, pero está inactivo en este fotograma) o se liberó (era el último fotograma, pero no hacia abajo).

Para realizar este tipo de lógica, deben crearse las variables locales que almacenan el fotograma anterior `GamePadState` y el actual `GamePadState` . En el ejemplo siguiente se muestra cómo almacenar y usar el del fotograma anterior `GamePadState` para implementar el salto:

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed =
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>Ejemplo: buscar botones

`GetCapabilities`se puede usar para comprobar si un controlador tiene cierto hardware, como un botón o un stick analógico determinados. En el código siguiente se muestra cómo comprobar los botones B e y en un controlador de un juego que requiere la presencia de ambos botones:

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

las aplicaciones de iOS admiten la entrada del controlador de juego inalámbrico.

> [!IMPORTANT]
> Los paquetes NuGet para monogame 3,5 no incluyen compatibilidad con los controladores de juegos inalámbricos. El uso de la clase de controlador para juegos en iOS requiere la compilación de monogame 3,5 desde el origen o el uso de los binarios de NuGet de monogame 3,6.

### <a name="ios-game-controller"></a>Dispositivo de juego iOS

La `GamePad` clase devuelve propiedades leídas de los controladores inalámbricos. Las propiedades de `GamePad` proporcionan una buena cobertura para el hardware del controlador iOS estándar, tal y como se muestra en el diagrama siguiente:

![Las propiedades del controlador de juegos proporcionan una buena cobertura para el hardware del controlador iOS estándar, tal como se muestra en este diagrama.](input-images/image1.png)

## <a name="apple-tv"></a>Apple TV

Apple TV Games puede usar los controladores de juegos remotos o inalámbricos de Siri para la entrada.

### <a name="siri-remote"></a>Siri remoto

*Siri Remote* es el dispositivo de entrada nativo de Apple TV. Aunque los valores del control remoto Siri se pueden leer a través de eventos (como se muestra en la [Guía de controladores remotos y Bluetooth de Siri](~/ios/tvos/platform/remote-bluetooth.md)), la `GamePad` clase puede devolver valores del remoto Siri.

Tenga en cuenta que `GamePad` solo puede leer la entrada del botón reproducir y la superficie táctil:

![Tenga en cuenta que el controlador de juegos solo puede leer entradas del botón reproducir y la superficie táctil](input-images/image2.png)

Dado que el movimiento de la superficie táctil se lee a través de la `DPad` propiedad, los valores de movimiento se informan mediante la `ButtonState` clase. En otras palabras, los valores solo están disponibles como `ButtonState.Pressed` o `ButtonState.Released` , en contraposición a valores numéricos o gestos.

### <a name="apple-tv-game-controller"></a>Controlador de juegos de Apple TV

Los dispositivos de juego para Apple TV se comportan de forma idéntica a los dispositivos de juego para aplicaciones iOS. Para obtener más información, vea la sección sobre el [dispositivo de juego iOS](#ios-game-controller) . 

## <a name="xbox-one"></a>Xbox One

La consola Xbox One admite la lectura de entrada desde un dispositivo de juego Xbox One.

### <a name="xbox-one-game-controller"></a>Dispositivo de juego Xbox One

La controladora Xbox One Game es el dispositivo de entrada más común para la Xbox One. La `GamePad` clase proporciona valores de entrada del hardware del controlador de juego.

![La clase de controlador para juegos proporciona valores de entrada del hardware del dispositivo de juego](input-images/image3.png)

## <a name="summary"></a>Resumen

En esta guía se proporciona información general sobre la clase de monogame `GamePad` , cómo implementar la lógica de lectura de entrada y diagramas de `GamePad` implementaciones comunes.

## <a name="related-links"></a>Vínculos relacionados

- [Controlador para juegos de monojuego](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
