---
title: SceneKit en Xamarin. iOS
description: En este documento se describe SceneKit, una API de gráficos de escena 3D que simplifica el trabajo con gráficos 3D al abstraer las complejidades de OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: d5aa7eb239b74437699aedb9699fefc862a3d345
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932397"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit en Xamarin. iOS

SceneKit es una API de gráfico de escena 3D que simplifica el trabajo con gráficos 3D. Se presentó por primera vez en OS X 10,8 y ahora se ha incorporado a iOS 8. Con SceneKit, la creación de visualizaciones 3D envolventes y juegos en 3D esporádicos no requiere experiencia en OpenGL. Al basarse en conceptos comunes de los gráficos de escenas, SceneKit abstrae las complejidades de OpenGL y OpenGL ES, lo que facilita enormemente la adición de contenido 3D a una aplicación. Sin embargo, si es un experto en OpenGL, SceneKit también tiene una gran compatibilidad para la vinculación directa con OpenGL. También incluye numerosas características que complementan los gráficos 3D, como la física, y se integran con muchos otros marcos de trabajo de Apple, como la animación básica, la imagen principal y el kit de Sprite.

SceneKit es muy fácil de trabajar con. Se trata de una API declarativa que se encarga de la representación. Solo tiene que configurar una escena, agregarle propiedades y SceneKit controlar la representación de la escena.

Para trabajar con SceneKit, cree un gráfico de escenas con la `SCNScene` clase. Una escena contiene una jerarquía de nodos, representada por instancias de `SCNNode` , que define ubicaciones en el espacio 3D. Cada nodo tiene propiedades como geometría, iluminación y materiales que afectan a su apariencia, tal como se muestra en la ilustración siguiente:

![La jerarquía SceneKit](scenekit-images/image7.png)

## <a name="create-a-scene"></a>Crear una escena

Para que una escena aparezca en la pantalla, agréguela a un `SCNView` mediante su asignación a la propiedad Scene de la vista. Además, si realiza cambios en la escena, `SCNView` se actualizará para mostrar los cambios.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Las escenas se pueden rellenar a partir de archivos exportados a través de una herramienta de modelado 3D o mediante programación a partir de primitivas geométricas. Por ejemplo, este es el procedimiento para crear una esfera y agregarla a la escena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Agregar luz

En este momento, la esfera no mostrará nada porque no hay ninguna luz en la escena. Al adjuntar `SCNLight` instancias a nodos, se crean luces en SceneKit. Hay varios tipos de luces que van desde diversas formas de iluminación direccional hasta la iluminación ambiente. Por ejemplo, el código siguiente crea una luz omnidireccional en el lateral de la esfera:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

La iluminación omnidireccional produce una reflexión difusa que da como resultado una iluminación uniforme, que es similar a la de una linterna. La creación de la luz ambiente es similar, aunque no tiene ninguna dirección, ya que destaca igualmente en todas las direcciones. Piense en ello como la iluminación del ambiente:)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Con las luces vigentes, la esfera ahora es visible en la escena.

![La esfera es visible en la escena cuando está encendida](scenekit-images/image8.png)

## <a name="adding-a-camera"></a>Agregar una cámara

La adición de una cámara (SCNCamera) a la escena cambia el punto de vista. El patrón para agregar la cámara es similar. Cree la cámara, adjúntela a un nodo y agregue el nodo a la escena.

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

Como puede ver en el código anterior, los objetos SceneKit se pueden crear mediante constructores o desde la creación de Factory Method. La primera permite el uso de la sintaxis de inicializador de C#, pero la que se va a usar es en gran medida una cuestión de preferencia.

Con la cámara en su lugar, toda la esfera es visible para el usuario:

![Toda la esfera es visible para el usuario](scenekit-images/image9.png)

También puede Agregar luces adicionales a la escena. Este es el aspecto con algunas luces más omnidireccionales:

![Esfera con algunas luces más omnidireccionales](scenekit-images/image10.png)

Además, al establecer `sceneView.AllowsCameraControl = true` , el usuario puede cambiar el punto de vista con un gesto táctil.

### <a name="materials"></a>Materiales

Los materiales se crean con la clase SCNMaterial. Por ejemplo, para agregar una imagen en la superficie de la esfera, establezca la imagen en el contenido *difuso* del material.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Esta capa la imagen se encuentra en el nodo, tal como se muestra a continuación:

![Capas de la imagen en la esfera](scenekit-images/image11.png)

También se puede establecer un material para responder a otros tipos de iluminación. Por ejemplo, el objeto puede hacerse brillante y tener su contenido especular establecido para mostrar la reflexión especular, lo que da lugar a una zona brillante en la superficie, como se muestra a continuación:

![Objeto que se ha hecho brillante con reflexión especular, lo que da lugar a una zona brillante en la superficie](scenekit-images/image12.png)

Los materiales son muy flexibles, lo que le permite conseguir mucho con muy poco código. Por ejemplo, en lugar de establecer la imagen en el contenido difuso, establézcala en el contenido reflectante.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Ahora, el Monkey parece sentarse visualmente dentro de la esfera, independientemente del punto de vista.

### <a name="animation"></a>Animación

SceneKit está diseñado para funcionar bien con la animación. Puede crear animaciones implícitas o explícitas, e incluso puede representar una escena de un árbol de capas de animación básico. Al crear una animación implícita, SceneKit proporciona su propia clase de transición, `SCNTransaction` .

Este es un ejemplo que gira la esfera:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Sin embargo, puede animar mucho más que la rotación. Muchas propiedades de SceneKit se pueden animar. Por ejemplo, el código siguiente anima el material `Shininess` para aumentar la reflexión especular.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit es muy sencillo de usar. Ofrece una gran cantidad de características adicionales, entre las que se incluyen las restricciones, la física, las acciones declarativas, el texto 3D, la compatibilidad con la profundidad de campo, la integración del kit de sprites y la integración de imagen principal para nombrar solo algunas.
