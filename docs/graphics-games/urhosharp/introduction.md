---
title: Introducción a UrhoSharp
description: En este documento se describe la estructura básica de una aplicación de UrhoSharp y vínculos a varias guías y aplicaciones de ejemplo que muestran cómo usar UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 37540053cee03a83582fe19ffb1dcf9e1cf4564c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011741"
---
# <a name="introduction-to-urhosharp"></a>Introducción a UrhoSharp

![Logotipo de UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp es un potente motor de juegos 3D para desarrolladores de Xamarin y .NET.  Es similar en el espíritu de SceneKit y SpriteKit de Apple e incluye física, navegación, redes y mucho más mientras sigue siendo multiplataforma.

Es un enlace de .NET al motor [Urho3D](https://urho3d.github.io/) y permite a los desarrolladores escribir código multiplataforma que puede tener como destino Android, iOS, Windows y Mac con el mismo código base y se puede representar en sistemas OpenGL y Direct3D.

UrhoSharp es un motor de juegos con una gran cantidad de funcionalidad:

- Representación gráfica en 3D eficaz
- Simulación física (mediante la biblioteca de viñetas)
- Control de escenas
- Compatibilidad con Await/Async
- API de acciones fáciles de obtener
- integración en 2D en escenas 3D
- Representación de fuentes con FreeType
- Capacidades de red de cliente y servidor
- Importar una amplia gama de activos (con la biblioteca de activos abiertos)
- Malla de navegación y pathfinding (mediante redifusión/desvío)
- Generación de casco convexo para la detección de colisiones (mediante StanHull)
- Reproducción de audio (con **libvorbis**)

## <a name="get-started"></a>Primeros pasos

UrhoSharp se distribuye de manera adecuada como un [paquete NuGet](https://www.nuget.org/) y se puede Agregar a los C# proyectos F# de o que tienen como destino Windows, Mac, Android o iOS.  NuGet incluye las bibliotecas necesarias para ejecutar el programa, así como los activos básicos (CoreData) que usa el motor.

### <a name="urho-as-a-portable-class-library"></a>Urho como una biblioteca de clases portable

El paquete Urho se puede usar desde un proyecto específico de la plataforma o desde un proyecto de biblioteca de clases portable, lo que le permite volver a usar todo el código en todas las plataformas.  Esto significa que todo lo que tendría que hacer en cada plataforma es escribir el punto de entrada específico de la plataforma y, a continuación, transferir el control al código de juego compartido.

### <a name="samples"></a>Ejemplos

Puede obtener un sabor para las funcionalidades de Urho abriendo en Visual Studio para Mac o Visual Studio la solución de ejemplo de:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La solución predeterminada contiene proyectos para Android, iOS, Windows y Mac.  Hemos estructurado esta solución para que tengamos un pequeño iniciador específico de la plataforma y todo el código de ejemplo y el código de juego se encuentren en una biblioteca de clases portable, lo que ilustra cómo maximizar la reutilización de código en todas las plataformas.

Consulte la página [Urho y su plataforma](~/graphics-games/urhosharp/platform/index.md) para obtener más información sobre cómo crear sus propias soluciones.

Dado que todos los ejemplos comparten un conjunto común de elementos de la interfaz de usuario, los ejemplos han abstraedo la configuración básica de este archivo:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Esto proporciona una clase base de ejemplo que controla algunas pulsaciones de teclas y eventos táctiles básicos, configura una cámara, proporciona elementos básicos de la interfaz de usuario y esto permite que cada ejemplo se Centre en la funcionalidad específica que se muestra.

En el ejemplo siguiente se muestra lo que el motor es capaz de hacer:

- [Samply Game](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clon sencillo de ShootySkies.

Mientras que los otros ejemplos muestran propiedades individuales de cada ejemplo.

## <a name="basic-structure"></a>Estructura básica

El juego debe subclaser la `Application` clase, aquí es donde configurará el juego (en el método `Setup`) e iniciará el juego (en el método `Start`).  A continuación, cree la interfaz de usuario principal.  Vamos a recorrer un ejemplo pequeño que muestra las API para configurar una escena 3D, algunos elementos de la interfaz de usuario y asociarle un comportamiento sencillo.

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

Si ejecuta esta aplicación, descubrirá rápidamente que el tiempo de ejecución se está quejando sobre los recursos que no están allí.  Lo que necesita hacer es crear una jerarquía en el proyecto que empiece por el nombre de directorio especial "Data" y, dentro de este, colocaría los recursos a los que hace referencia en el programa.  A continuación, debe establecer en las propiedades del elemento para cada activo el "copiar en el directorio de salida" a "copiar si es posterior", lo que garantizará que los datos están allí.

Vamos a explicar lo que está ocurriendo aquí.

Para iniciar la aplicación, llame a la función de inicialización del motor, seguido de la creación de una nueva instancia de la clase de aplicación, similar a la siguiente:

```csharp
new MySample().Run();
```

El tiempo de ejecución invocará los métodos `Setup` y `Start` automáticamente.  Si invalida `Setup` puede configurar los parámetros del motor (no se muestran en este ejemplo).

Debe invalidar `Start` ya que esto iniciará el juego.  En este método se cargarán los recursos, se conectarán los controladores de eventos, se configurará la escena y se iniciarán las acciones que desee.  En nuestro ejemplo, creamos un poco de interfaz de usuario para mostrar al usuario, así como para configurar una escena 3D.

En el siguiente fragmento de código se usa el marco de interfaz de usuario para crear un elemento de texto y agregarlo a la aplicación:

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

El marco de interfaz de usuario está ahí para proporcionar una interfaz de usuario muy sencilla y funciona agregando nuevos nodos al nodo `UI.Root`.

En la segunda parte de nuestro ejemplo se configura la escena principal.  Esto implica una serie de pasos, la creación de una escena 3D, la creación de un cuadro 3D en la pantalla, la adición de una luz, una cámara y una ventanilla.  Estos se exploran con más detalle en la sección [escena, nodos, componentes y cámaras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La tercera parte de nuestro ejemplo desencadena un par de acciones.  Las acciones son recetas que describen un efecto determinado y, una vez creadas, las puede ejecutar un nodo a petición llamando al método `RunActionAsync` en un `Node`.

La primera acción escala el cuadro con un efecto de rebote y el segundo gira el cuadro siempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Lo anterior muestra cómo la primera acción que se crea es una acción `ScaleTo`, es simplemente una receta que indica que se desea escalar un segundo hacia el valor de la propiedad Scale de un nodo.  Esta acción, a su vez, se ajusta en torno a una acción de entrada y salida lenta, la acción de `EaseBounceOut`.  Las acciones de entradas y salidas lentas distorsionan la ejecución lineal de una acción y aplican un efecto; en este caso, proporciona el efecto de rebote.
Por lo tanto, nuestra receta podría escribirse de la siguiente manera:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Una vez creada la receta, se ejecuta la receta:

```csharp
await boxNode.RunActionsAsync (recipe)
```

La función Await indica que el deseará reanudar la ejecución después de esta línea cuando se complete la acción.  Una vez completada la acción, se desencadena la segunda animación.

El documento [uso de UrhoSharp](~/graphics-games/urhosharp/using.md) explora en profundidad los conceptos que se encuentran detrás de Urho y cómo estructurar el código para compilar un juego.

## <a name="copyrights"></a>Derechos

Esta documentación contiene contenido original de Xamarin Inc, pero se basa en gran medida en la documentación de código abierto para el proyecto Urho3D y contiene capturas de pantallas del proyecto Cocos2D.
