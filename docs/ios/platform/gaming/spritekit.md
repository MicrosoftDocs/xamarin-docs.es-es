---
title: SpriteKit en Xamarin. iOS
description: En este documento se describe SpriteKit, el marco de gráficos 2D de Apple que se integra con SceneKit, incorpora la física y la animación, incluye compatibilidad con la iluminación y el sombreado, etc. SpriteKit se puede usar para crear juegos en 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: d2466de4891c289f4686c37bc9fe73c24a5a48ca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753062"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit en Xamarin. iOS

SpriteKit, el marco de gráficos 2D de Apple, tiene algunas características nuevas interesantes en iOS 8 y OS X Yosemite. Esto incluye la integración con SceneKit, compatibilidad del sombreador, iluminación, sombras, restricciones, generación normal de mapas y mejoras físicas. En concreto, las nuevas características de la física hacen que sea muy fácil agregar efectos realistas a un juego.

## <a name="physics-bodies"></a>Cuerpos físicos

SpriteKit incluye una API de física de cuerpo 2D y rígida. Cada Sprite tiene un cuerpo físico asociado (`SKPhysicsBody`) que define las propiedades de la física, como la masa y la fricción, así como la geometría del cuerpo del mundo físico.

## <a name="creating-a-physics-body-from-a-texture"></a>Crear un cuerpo físico a partir de una textura
SpriteKit admite ahora la derivación del cuerpo de la física de un Sprite a partir de su textura. Esto facilita la implementación de colisiones que parecen más naturales.

Por ejemplo, observe en la siguiente colisión cómo los plátanos y Monkey entran en conflicto casi en la superficie de cada imagen:

![](spritekit-images/image13.png "El plátano y Monkey entran en conflicto casi en la superficie de cada imagen.")

SpriteKit hace posible la creación de este cuerpo físico con una sola línea de código. Simplemente llame `SKPhysicsBody.Create` a con la textura y el tamaño: Sprite. PhysicsBody = SKPhysicsBody. Create (Sprite. Texture, Sprite. Tamaño);

## <a name="alpha-threshold"></a>Umbral alfa

Además de establecer simplemente la `PhysicsBody` propiedad directamente en el objeto Geometry derivado de la textura, las aplicaciones pueden establecer y umbral alfa para controlar cómo se deriva la geometría. 

El umbral alfa define el valor alfa mínimo que debe tener un píxel para incluirse en el cuerpo físico resultante. Por ejemplo, el código siguiente da como resultado un cuerpo de física ligeramente diferente:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

El efecto de ajustar el umbral alfa como este ajusta la colisión anterior, de modo que el Monkey se recurra al conflicto con el plátano:

![](spritekit-images/image14.png "El Monkey se sitúa cuando entra en conflicto con el plátano")

## <a name="physics-fields"></a>Campos de física

Otra excelente adición a SpriteKit es el nuevo soporte de campo físico. Estos permiten agregar elementos, como los campos de Vortex, los campos de gravedad radial y los campos de muelle por nombrar solo unos pocos.

Los campos de física se crean mediante la clase SKFieldNode, que se agrega a una escena como cualquier `SKNode`otro. Hay una variedad de métodos `SKFieldNode` de generador para crear distintos campos físicos. Puede crear un campo de Spring llamando a `SKFieldNode.CreateSpringField()`, un campo de gravedad radial llamando `SKFieldNode.CreateRadialGravityField()`a, y así sucesivamente.

`SKFieldNode`también tiene propiedades para controlar atributos de campo como la intensidad del campo, la región del campo y la atenuación de las fuerzas del campo.

## <a name="spring-field"></a>Campo Spring

Por ejemplo, el código siguiente crea un campo Spring y lo agrega a la escena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

Después, puede Agregar sprites y establecer sus `PhysicsBody` propiedades para que el campo físico afecte a los sprites, como hace el código siguiente cuando el usuario toca la pantalla:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

Esto hace que los plátanos oscilan como un muelle alrededor del nodo de campo:

![](spritekit-images/image15.png "Los plátanos oscilan como un muelle alrededor del nodo de campo")

## <a name="radial-gravity-field"></a>Campo de gravedad radial

Agregar un campo diferente es similar. Por ejemplo, el código siguiente crea un campo de gravedad radial:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Esto da como resultado un campo de fuerza diferente, en el que los plátanos se extraen radialmente sobre el campo:

![](spritekit-images/image16.png "Los plátanos se extraen en torno al campo")
