---
title: API de juegos de iOS en Xamarin. iOS
description: En este artículo se tratan las nuevas mejoras de juegos que proporciona iOS 9 que se pueden usar para mejorar las características de audio y gráficos del juego de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: fa78a596495b22ebb2c8b148aadb76261845ccdc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281261"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>API de juegos de iOS en Xamarin. iOS

_En este artículo se tratan las nuevas mejoras de juegos que proporciona iOS 9 que se pueden usar para mejorar las características de audio y gráficos del juego de Xamarin. iOS._

Apple ha realizado varias mejoras tecnológicas en las API de juegos de iOS 9 que facilitan la implementación de gráficos y audio de juegos en una aplicación de Xamarin. iOS.
Entre ellas se incluyen la facilidad de desarrollo a través de los marcos de trabajo de alto nivel y el aprovechamiento de la capacidad de la GPU del dispositivo iOS para mejorar la velocidad y las capacidades gráficas.

[![](images/flocking01.png "Un ejemplo de una aplicación que ejecuta la reutilización")](images/flocking01.png#lightbox)

Esto incluye GameplayKit, ReplayKit, e/s de modelo, MetalKit y los sombreadores de rendimiento de metal junto con las nuevas características mejoradas de metal, SceneKit y SpriteKit.

En este artículo se presentan todas las formas de mejorar el juego de Xamarin. iOS con las nuevas mejoras de los juegos de iOS 9:

## <a name="introducing-gameplaykit"></a>Introducción a GameplayKit

El nuevo marco de trabajo de GameplayKit de Apple proporciona un conjunto de tecnologías que facilitan la creación de juegos para dispositivos iOS, ya que se reduce la cantidad de código común y repetitivo necesario para la implementación. GameplayKit proporciona herramientas para desarrollar las mecánicas de juego que se pueden combinar fácilmente con un motor gráfico (como SceneKit o SpriteKit) para ofrecer rápidamente un juego completado.

GameplayKit incluye varios algoritmos comunes de Game Play, como:

- Una simulación de agente basada en el comportamiento que le permite definir los movimientos y los objetivos que el AI va a seguir automáticamente.
- Una inteligencia artificial de MinMax para la reproducción de juegos basada en turnos.
- Un sistema de reglas para la lógica de juego controlada por datos con razonamiento aproximado para proporcionar un comportamiento emergente.

Además, GameplayKit toma un enfoque de bloques de creación para el desarrollo de juegos mediante el uso de una arquitectura modular que proporciona las siguientes características:

- Equipo de estado para administrar sistemas complejos basados en código de procedimientos en la reproducción de juegos.
- Herramientas para proporcionar una reproducción de juego aleatoria y una impredecible sin causar problemas de depuración.
- Arquitectura de basada en entidades reutilizable y con componentes.

Para más información sobre GameplayKit, consulte la guía de [programación de GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) de Apple y [referencia de GameplayKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Ejemplos de GameplayKit

Echemos un vistazo a la implementación de algunas mecánicas sencillas de reproducción en una aplicación de Xamarin. iOS con Game Play kit.

### <a name="pathfinding"></a>Pathfinding

Pathfinding es la capacidad de un elemento de AI de un juego para encontrar su forma de evitar el tablero de juego.
Por ejemplo, un enemigo de 2D está buscando su camino a través de un laberinto o un carácter 3D a través de un terreno mundial de la primera persona superpuesto.

Considere el siguiente mapa:

[![](images/gkpathfindpath.png "Un mapa pathfinding de ejemplo")](images/gkpathfindpath.png#lightbox)

Mediante pathfinding, C# este código puede encontrar una manera a través del mapa:

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>Sistema experto clásico

El siguiente fragmento de C# código muestra cómo se puede usar GameplayKit para implementar un sistema experto clásico:

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

En función de un conjunto determinado de reglas`GKRule`() y un conjunto conocido de entradas, el sistema experto`GKRuleSystem`() creará una salida predecible`fizzbuzz` (para nuestro ejemplo anterior).

### <a name="flocking"></a>Rebaño

La agrupación permite que un grupo de entidades de juegos controladas por AI se comporte como un rebaño, en el que el grupo responde a los movimientos y acciones de una entidad de plomo como un rebaño de pájaros en el vuelo o una escuela de natación de peces.

El siguiente fragmento de C# código implementa el comportamiento de rebaño mediante GameplayKit y SpriteKit para la presentación de gráficos:

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;


        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }


        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

A continuación, implemente esta escena en un controlador de vista:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

Cuando se ejecuta, el pequeño _"boids"_ animado se reproducirá en torno a las pulsaciones de dedos:

[![](images/flocking01.png "Los pequeños boids animados supondrán alrededor de las pulsaciones de dedos")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Otros ejemplos de Apple

Además de los ejemplos presentados anteriormente, Apple ha proporcionado las siguientes aplicaciones de ejemplo que se pueden transcodificar en C# y Xamarin. iOS:

- [FourInARow: Uso del estratega de GameplayKit MinMax para el oponente AI](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: Uso del sistema de agentes en GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Creación de un juego multiplataforma con SpriteKit y GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

En iOS 9, Apple ha realizado varios cambios y adiciones a metal para proporcionar acceso de baja sobrecarga a la GPU. Con metal puede maximizar los gráficos y el potencial de las aplicaciones iOS.

El marco de metal incluye las siguientes características nuevas:

- Nuevas texturas de galería de símbolos de profundidad y privada para OS X.
- Mejora de la calidad de sombra con compresión de profundidad y valores de la galería de símbolos frontal y posterior.
- Mejoras en el lenguaje de sombreado de metal y en la biblioteca estándar de metal.
- Los sombreadores de cálculo admiten una gama más amplia de formatos de píxeles.

### <a name="the-metalkit-framework"></a>El marco de MetalKit

El marco de trabajo de MetalKit proporciona un conjunto de características y clases de utilidad que reducen la cantidad de trabajo necesario para usar metal en una aplicación de iOS. MetalKit ofrece compatibilidad en tres áreas clave:

1. La carga de texturas asincrónica desde una variedad de orígenes incluye formatos comunes como PNG, JPEG, KTX y PVR.
2. Acceso sencillo a los recursos basados en e/s de modelo para el control de modelos específicos de metal. Estas características se han optimizado para proporcionar una transferencia de datos eficaz entre mallas de e/s de modelo y búferes de metal.
3. Vistas de metal predefinidas y administración de vistas que reducen en gran medida la cantidad de código necesario para mostrar las representaciones de gráficos en una aplicación de iOS.

Para obtener más información sobre MetalKit, consulte la guía de [referencia de MetalKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)de Apple, [Guía de programación de metal](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), referencia del marco de [metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) y [sombreado de metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Marco de sombreadores de rendimiento de metal

El marco de trabajo del sombreador de metal performance proporciona un conjunto muy optimizado de gráficos y sombreadores basados en el cálculo para su uso en las aplicaciones de iOS basadas en metal. Cada sombreador del marco del sombreador de rendimiento de metal se ha optimizado específicamente para proporcionar un alto rendimiento en las GPU de iOS compatibles con el metal.

Mediante el uso de las clases de sombreador de rendimiento de metal, puede lograr el mayor rendimiento posible en cada GPU de iOS específica sin tener que tener como destino y mantener bases de código individuales. Los sombreadores de rendimiento de metal se pueden usar con cualquier recurso de metal, como texturas y búferes.

El marco del sombreador de rendimiento de metal proporciona un conjunto de sombreadores comunes, como:

- **Desenfoque gausiano** (`MPSImageGaussianBlur`)
- **Detección de Sobel Edge** (`MPSImageSobel`)
- **Histograma de imagen** (`MPSImageHistogram`)

Para obtener más información, consulte la [Guía del lenguaje de sombreado de metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)de Apple.

## <a name="introducing-model-io"></a>Presentación de e/s de modelo

El marco de e/s de modelo de Apple proporciona una comprensión profunda de los activos 3D (como los modelos y sus recursos relacionados). La e/s del modelo proporciona juegos de iOS con materiales, modelos e iluminación basados en el físico que se pueden usar con GameplayKit, metal y SceneKit.

Con e/s de modelo, puede admitir los siguientes tipos de tareas:

- Importe la iluminación, los materiales, los datos de malla, la configuración de la cámara y otra información basada en escenas de diversos formatos de motor de juegos y software conocidos.
- Procesar o generar información basada en escenas, como crear cúpulas de cielo con textura de procedimientos o iluminación de hornear en una malla.
- Funciona con MetalKit, SceneKit y GLKit para cargar eficazmente activos de juegos en búferes de GPU para su representación.
- Exportar información basada en escenas a una variedad de formatos populares de software y de motor de juegos.

Para obtener más información sobre la e/s del modelo, consulte [Referencia del marco de e/s del modelo](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421) de Apple.

## <a name="introducing-replaykit"></a>Introducción a ReplayKit

El nuevo marco de ReplayKit de Apple le permite agregar fácilmente grabaciones de juegos a su juego iOS y permitir al usuario editar y compartir este vídeo de forma rápida y sencilla desde la aplicación.

Para obtener más información, consulte [redes sociales de Apple con ReplayKit y Game Center video](https://developer.apple.com/videos/wwdc/2015/?id=605) y su [DemoBots: Creación de un juego multiplataforma con una aplicación](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) de ejemplo de SpriteKit y GameplayKit.

## <a name="scenekit"></a>SceneKit

El kit de escenas es una API de gráficos de escenas 3D que simplifica el trabajo con gráficos 3D. Se presentó por primera vez en OS X 10,8 y ahora se ha incorporado a iOS 8. Con el kit de escenas, crear visualizaciones 3D envolventes y juegos en 3D esporádicos no requiere experiencia en OpenGL. Al basarse en conceptos comunes de los gráficos de escenas, el kit de escenas abstrae las complejidades de OpenGL y OpenGL ES, lo que facilita enormemente la adición de contenido 3D a una aplicación. Sin embargo, si es un experto en OpenGL, el kit de escenas también tiene una gran compatibilidad para la vinculación directa con OpenGL. También incluye numerosas características que complementan los gráficos 3D, como la física, y se integran con muchos otros marcos de trabajo de Apple, como la animación básica, la imagen principal y el kit de Sprite.

Para obtener más información, consulte nuestra documentación de [SceneKit](~/ios/platform/gaming/scenekit.md) .

### <a name="scenekit-changes"></a>SceneKit cambios

Apple ha agregado las siguientes características nuevas a SceneKit para iOS 9:

- Xcode ofrece ahora un editor de escenas que permite crear rápidamente juegos y aplicaciones 3D interactivas mediante la edición de escenas directamente desde Xcode.
- Las `SCNView` clases `SCNSceneRenderer` y se pueden usar para habilitar la representación de metal (en dispositivos iOS compatibles).
- Las `SCNAudioPlayer` clases `SCNNode` y se pueden usar para agregar efectos de audio espacial que realizan un seguimiento automático de una posición del reproductor en una aplicación de iOS.

Para obtener más información, consulte la [documentación de SceneKit](~/ios/platform/introduction-to-ios8.md#scenekit) y la [referencia de SceneKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) de Apple y [Fox: Creación de un juego de SceneKit con el proyecto](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) de ejemplo Xcode Scene editor.

## <a name="spritekit"></a>SpriteKit

El kit de sprites, el marco de juego de 2D de Apple, tiene algunas nuevas características interesantes en iOS 8 y OS X Yosemite. Entre ellas se incluyen la integración con el kit de escenas, compatibilidad con el sombreador, iluminación, sombras, restricciones, generación normal de mapas y mejoras físicas. En concreto, las nuevas características de la física hacen que sea muy fácil agregar efectos realistas a un juego.

Para obtener más información, consulte nuestra documentación de [SpriteKit](~/ios/platform/gaming/spritekit.md) .

### <a name="spritekit-changes"></a>SpriteKit cambios

Apple ha agregado las siguientes características nuevas a SpriteKit para iOS 9:

- Efecto de audio espacial que realiza un seguimiento automático de la posición `SKAudioNode` del jugador con la clase.
- Xcode ahora incluye un editor de escenas y un editor de acciones para facilitar la creación de juegos y aplicaciones en 2D.
- Compatibilidad con el desplazamiento sencillo con objetos nuevos de nodos`SKCameraNode`de cámara ().
- En los dispositivos iOS que admiten metal, SpriteKit lo usará automáticamente para la representación, incluso si ya usaba los sombreadores de OpenGL ES personalizados.

Para obtener más información, consulte nuestra [documentación de SpriteKit](~/ios/platform/introduction-to-ios8.md#spritekit) [referencia de SpriteKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) de Apple [y su DemoBots: Creación de un juego multiplataforma con una aplicación](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) de ejemplo de SpriteKit y GameplayKit.

## <a name="summary"></a>Resumen

En este artículo se han tratado las nuevas características de juegos que iOS 9 proporciona para las aplicaciones de Xamarin. iOS.
Incorporó GameplayKit y e/s de modelo. las principales mejoras de metal; y las nuevas características de SceneKit y SpriteKit.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
