---
title: Introducción a ARKit en Xamarin. iOS
description: En este documento se describe la realidad aumentada en iOS 11 con ARKit. En él se describe cómo agregar un modelo 3D a una aplicación, cómo configurar la vista, cómo implementar un delegado de sesión, cómo colocar el modelo 3D en el mundo y cómo pausar la sesión de realidad aumentada.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 0094a496ce99addb08648431d993bd4afddca2f4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032246"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Introducción a ARKit en Xamarin. iOS

_Realidad aumentada para iOS 11_

ARKit permite una amplia variedad de aplicaciones y juegos de realidad aumentada. En esta sección se tratan los siguientes temas:

- [Introducción con ARKit](#gettingstarted)
- [Uso de ARKit con UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Introducción con ARKit

Para empezar a trabajar con la realidad aumentada, las siguientes instrucciones describen una aplicación sencilla: posicionamiento de un modelo 3D y permitir que ARKit mantenga el modelo en su lugar con la funcionalidad de seguimiento.

![Modelo 3D jet flotante en imagen de cámara](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. agregar un modelo 3D

Los recursos se deben agregar al proyecto con la acción de compilación **SceneKitAsset** .

![SceneKit recursos en un proyecto](images/scene-assets.png)

### <a name="2-configure-the-view"></a>2. configurar la vista

En el método `ViewDidLoad` del controlador de vista, cargue el recurso de la escena y establezca la propiedad `Scene` en la vista:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. implementar opcionalmente un delegado de sesión

Aunque no es necesario para los casos sencillos, la implementación de un delegado de sesión puede ser útil para depurar el estado de la sesión de ARKit (y, en aplicaciones reales, proporcionar comentarios al usuario). Cree un delegado simple mediante el código siguiente:

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

Asigne el delegado en el método `ViewDidLoad`:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. colocar el modelo 3D en el mundo

En `ViewWillAppear`, el código siguiente establece una sesión de ARKit y establece la posición del modelo 3D en el espacio relativo a la cámara del dispositivo:

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

Cada vez que se ejecuta o se reanuda la aplicación, el modelo 3D se colocará delante de la cámara. Una vez colocado el modelo, mueva la cámara y mire como ARKit mantiene el modelo colocado.

### <a name="5-pause-the-augmented-reality-session"></a>5. pausar la sesión de realidad aumentada

Es recomendable pausar la sesión de ARKit cuando el controlador de vista no esté visible (en el método `ViewWillDisappear`:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Resumen

El código anterior da como resultado una aplicación ARKit simple. Los ejemplos más complejos esperan que el controlador de vistas que hospeda la sesión de realidad aumentada implemente `IARSCNViewDelegate`y que se implementen métodos adicionales.

ARKit proporciona muchas características más sofisticadas, como el seguimiento de superficie y la interacción del usuario. Vea la [demostración de UrhoSharp](urhosharp.md) para obtener un ejemplo de cómo combinar el seguimiento de ARKit con UrhoSharp.

## <a name="related-links"></a>Vínculos relacionados

- [Realidad aumentada (Apple)](https://developer.apple.com/arkit/)
- [Uso de ARKit con UrhoSharp](urhosharp.md)
- [Ejemplo de ARKit simple (jet)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit colocar objetos (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [Introducción a ARKit-aumento de la realidad de iOS (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/602/)
