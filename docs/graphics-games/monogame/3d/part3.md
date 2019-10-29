---
title: Coordenadas 3D en monogame
description: Entender el sistema de coordenadas 3D es un paso importante para desarrollar juegos 3D. Monogame proporciona una serie de clases para colocar, orientar y escalar objetos en el espacio 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: a38f4b81f684d6d416e6abe017bc463e3097c6b1
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980865"
---
# <a name="3d-coordinates-in-monogame"></a>Coordenadas 3D en monogame

_Entender el sistema de coordenadas 3D es un paso importante para desarrollar juegos 3D. Monogame proporciona una serie de clases para colocar, orientar y escalar objetos en el espacio 3D._

El desarrollo de juegos 3D requiere un conocimiento de cómo manipular objetos en un sistema de coordenadas 3D. En este tutorial se explica cómo manipular objetos visuales (específicamente un modelo). Basaremos en los conceptos de control de un modelo para crear una clase de cámara 3D.

Los conceptos presentados se originan a partir de álgebra lineal, pero adoptaremos un enfoque práctico para que cualquier usuario sin un fondo matemático fuerte pueda aplicar estos conceptos en sus propios juegos.

Trataremos los temas siguientes:

- Crear un proyecto
- Creación de una entidad robot
- Traslado de la entidad robot
- Multiplicación de matrices
- Creación de la entidad Camera
- Mover la cámara con entrada

Una vez finalizado, tendremos un proyecto con un robot moviéndose en un círculo y una cámara que se puede controlar mediante la entrada táctil:

![](part3-images/image1.gif "Once finished, the app will include a project with a robot moving in a circle and a camera which can be controlled by touch input")

## <a name="creating-a-project"></a>Crear un proyecto

Este tutorial se centra en mover objetos en el espacio 3D. Comenzaremos con el proyecto para representar modelos y matrices de vértices [que se pueden encontrar aquí](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/). Una vez descargado, descomprima y abra el proyecto para asegurarse de que se ejecuta y debemos ver lo siguiente:

![](part3-images/image2.png "Once downloaded, unzip and open the project to make sure it runs and this view should be displayed")

## <a name="creating-a-robot-entity"></a>Creación de una entidad robot

Antes de empezar a mover el robot, crearemos una clase `Robot` para que contenga la lógica de dibujo y movimiento. Los desarrolladores de juegos hacen referencia a esta encapsulación de lógica y datos como una *entidad*.

Agregue un nuevo archivo de clase vacía a la biblioteca de clases portable **MonoGame3D** (no a ModelAndVerts. Android específico de la plataforma). Asígnele el nombre **robot** y haga clic en **nuevo**:

![](part3-images/image3.png "Name it Robot and click New")

Modifique la clase `Robot` como se indica a continuación:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

El código `Robot` es esencialmente el mismo código en `Game1` para dibujar una `Model`. Para ver una revisión sobre la carga y el dibujo de `Model`, consulte [esta guía sobre cómo trabajar con modelos](~/graphics-games/monogame/3d/part1.md). Ahora podemos quitar todos los `Model` código de carga y representación de `Game1`y reemplazarlo por una instancia de `Robot`:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Si ejecutamos el código ahora, tendremos una escena con un solo robot que se dibuja principalmente en el suelo:

![](part3-images/image4.png "If the code is run now, the app will display a scene with only one robot which is drawn mostly under the floor")

## <a name="moving-the-robot"></a>Movimiento del robot

Ahora que tenemos una clase `Robot`, podemos agregar lógica de movimiento al robot. En este caso, simplemente haremos que el robot se mueva en un círculo según el tiempo de juego. Se trata de una implementación algo práctica para un juego real, ya que un carácter normalmente responde a la entrada o a la inteligencia artificial, pero proporciona un entorno para que podamos explorar el posicionamiento y la rotación 3D.

La única información que se necesitará desde fuera de la clase `Robot` es el tiempo de juego actual. Vamos a agregar un método `Update` que tomará un parámetro `GameTime`. Este parámetro `GameTime` se usará para incrementar una variable de ángulo que se utilizará para determinar la posición final del robot.

En primer lugar, vamos a agregar el campo de ángulo a la clase `Robot` en el campo `model`:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Ahora podemos incrementar este valor en una función `Update`:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Necesitamos asegurarse de que se llama al método `Update` desde `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Por supuesto, en este momento el campo de ángulo no hace nada, es necesario escribir código para usarlo. Modificaremos el método de `Draw` para que podamos calcular el `Matrix` del mundo en un método dedicado: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

A continuación, implementaremos el método `GetWorldMatrix` en la clase `Robot`:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

El resultado de la ejecución de este código hace que el robot se mueva en un círculo:

![](part3-images/image5.gif "Running this code results in the robot moving in a circle")

## <a name="matrix-multiplication"></a>Multiplicación de matrices

El código anterior gira el robot creando un `Matrix` en el método `GetWorldMatrix`. El struct `Matrix` contiene 16 valores float que se pueden usar para traducir (establecer posición), girar y escalar (tamaño establecido). Cuando se asigna la propiedad `effect.World`, se indica al sistema de representación subyacente cómo colocar, cambiar el tamaño y orientar todo lo que se va a dibujar (una `Model` o geometría de los vértices). 

Afortunadamente, el struct `Matrix` incluye una serie de métodos que simplifican la creación de tipos comunes de matrices. La primera utilizada en el código anterior es `Matrix.CreateTranslation`. La *conversión* de términos matemáticos hace referencia a una operación que da como resultado un punto (o en nuestro caso un modelo) que se mueve de una ubicación a otra sin ninguna otra modificación (como girar o cambiar el tamaño). La función toma un valor X, y y Z para la traducción. Si vemos nuestra escena de arriba abajo, nuestro método de `CreateTranslation` (en aislamiento) realiza lo siguiente:

![](part3-images/image6.png "The CreateTranslation method in isolation performs this action")

La segunda matriz que se crea es una matriz de rotación mediante el `CreateRotationZ` matriz. Este es uno de los tres métodos que se pueden usar para crear la rotación:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Cada método crea una matriz de rotación girando sobre un eje determinado. En nuestro caso, estamos rotando sobre el eje Z, que señala "hacia arriba". Lo siguiente puede ayudar a visualizar cómo funciona la rotación basada en ejes:

![](part3-images/image7.png "This can help visualize how axis-based rotation works")

También usamos el método `CreateRotationZ` con el campo ángulo, que se incrementa con el tiempo debido a que se llama al método `Update`. El resultado es que el método `CreateRotationZ` hace que nuestro robot gire alrededor del origen a medida que pasa el tiempo.

La última línea de código combina las dos matrices en una:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Esto se conoce como multiplicación de matrices, que funciona ligeramente diferente de la multiplicación normal. La *propiedad conmutable de la multiplicación* indica que el orden de los números en una operación de multiplicación no cambia el resultado. Es decir, 3 \* 4 es equivalente a 4 \* 3. La multiplicación de matrices difiere en que no es conmutativa. Es decir, la línea anterior se puede leer como "aplicar translationMatrix para trasladar el modelo y, a continuación, girar todo aplicando rotationMatrix". Podríamos visualizar el modo en que la línea anterior afecta a la posición y la rotación de la manera siguiente:

![](part3-images/image8.png "A visualization pf the way that the above line affects the position and rotation")

Para ayudar a comprender cómo el orden de multiplicación de la matriz puede afectar al resultado, tenga en cuenta lo siguiente, donde se invierte la multiplicación de la matriz:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

El código anterior rotaría primero el modelo en contexto y, a continuación, lo traduciría:

![](part3-images/image9.png "The code above would first rotate the model in-place, then translate it")

Si ejecutamos el código con la multiplicación invertida, observamos que, puesto que el giro se aplica primero, solo afecta a la orientación del modelo y la posición del modelo sigue siendo la misma. En otras palabras, el modelo gira en su lugar:

![](part3-images/image10.gif "The model rotates in place")

## <a name="creating-the-camera-entity"></a>Creación de la entidad Camera

La entidad `Camera` contendrá toda la lógica necesaria para realizar el movimiento basado en entrada y proporcionar propiedades para asignar propiedades en la clase `BasicEffect`.

En primer lugar, se implementará una cámara estática (sin movimiento basado en entrada) y se integrará en nuestro proyecto existente. Agregue una nueva clase a la biblioteca de clases portable **MonoGame3D** (el mismo proyecto con `Robot.cs`) y asígnele el nombre **Camera**. Reemplace el contenido del archivo por el código siguiente:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

El código anterior es muy similar al código de `Game1` y `Robot` que asignan las matrices en `BasicEffect`. 

Ahora podemos integrar la nueva clase de `Camera` en nuestros proyectos existentes. En primer lugar, modificaremos la clase `Robot` para tomar una instancia de `Camera` en su método `Draw`, lo que eliminará una gran cantidad de código duplicado. Reemplace el método `Robot.Draw` por lo siguiente:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

A continuación, modifique el archivo de `Game1.cs`:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Las modificaciones realizadas en la `Game1` de la versión anterior (que se identifican con `// New camera code`) son:

- `Camera` campo en `Game1`
- creación de instancias de `Camera` en `Game1.Initialize`
- `Camera.Update` llamada en `Game1.Update`
- `Robot.Draw` ahora toma un parámetro `Camera`
- ahora `Game1.Draw` usa `Camera.ViewMatrix` y `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Mover la cámara con entrada

Hasta ahora, hemos agregado una entidad `Camera` pero no hemos hecho nada con ella para cambiar el comportamiento de tiempo de ejecución. Agregaremos el comportamiento que permite al usuario:

- Toque el lado izquierdo de la pantalla para girar la cámara hacia la izquierda
- Toque el lado derecho de la pantalla para convertir la cámara a la derecha
- Toque el centro de la pantalla para avanzar la cámara

### <a name="making-lookat-relative"></a>Conversión de lookAt en relativa

En primer lugar, actualizaremos la clase `Camera` para incluir un `angle` campo que se usará para establecer la dirección a la que se enfrenta el `Camera`. Actualmente, nuestro `Camera` determina la dirección a la que se enfrenta a través del `lookAtVector`local, que se asigna a `Vector3.Zero`. En otras palabras, nuestra `Camera` siempre examina el origen. Si se mueve la cámara, también cambiará el ángulo al que está orientada la cámara:

![](part3-images/image11.gif "If the Camera moves, then the angle that the camera is facing will also change")

Queremos que el `Camera` esté orientado a la misma dirección, independientemente de su posición, al menos hasta que implementemos la lógica para rotar el `Camera` mediante la entrada. El primer cambio será ajustar la variable de `lookAtVector` para que se base en la ubicación actual, en lugar de examinar una posición absoluta:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

Esto da como resultado el `Camera` ver el mundo directo. Tenga en cuenta que el valor inicial de `position` se ha modificado a `(0, 20, 10)` para que el `Camera` esté centrado en el eje X. La ejecución del juego muestra:

![](part3-images/image12.png "Running the game displays this view")

### <a name="creating-an-angle-variable"></a>Crear una variable de ángulo

La variable `lookAtVector` controla el ángulo que está viendo nuestra cámara. Actualmente se corrige para ver el eje Y negativo y ligeramente inclinado hacia abajo (del valor `-.5f` Z). Vamos a crear una variable `angle` que se usará para ajustar la propiedad `lookAtVector`. 

En las secciones anteriores de este tutorial, mostramos que las matrices se pueden usar para girar la forma en que se dibujan los objetos. También podemos usar matrices para girar vectores como el `lookAtVector` mediante el método `Vector3.Transform`. 

Agregue un campo de `angle` y modifique la propiedad `ViewMatrix` como se indica a continuación:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Leer entrada

La entidad de `Camera` ahora se puede controlar por completo a través de sus variables de posición y ángulo, solo tenemos que cambiarlas según la entrada.

En primer lugar, obteneremos el estado `TouchPanel` para encontrar el lugar en el que el usuario toca la pantalla. Para obtener más información sobre el uso de la clase `TouchPanel`, consulte [la referencia de la API de Touchpanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Si el usuario se toca a la izquierda en tercer lugar, ajustaremos el valor `angle` para que el `Camera` rote hacia la izquierda y, si el usuario se toca en el tercer lugar, giraremos de la otra manera. Si el usuario está tocando en el tercio medio de la pantalla, se moverá el `Camera` hacia delante.

En primer lugar, agregue una instrucción using para calificar las clases `TouchPanel` y `TouchCollection` en `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

A continuación, modifique el método `Update` para leer el panel táctil y ajustar las variables `angle` y `position` correctamente:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

Ahora, el `Camera` responderá a la entrada táctil:

![](part3-images/image1.gif "Now the Camera will respond to touch input")

El método Update comienza llamando a `TouchPanel.GetState`, que devuelve una colección de toques. Aunque `TouchPanel.GetState` puede devolver varios puntos táctiles, solo nos preocuparemos por la simplicidad.

Si el usuario toca la pantalla, el código comprueba si la primera entrada está en el tercio izquierdo, medio o derecho de la pantalla. Los tercios izquierdo y derecho giran la cámara aumentando o disminuyendo la variable de `angle` según el valor `TotalSeconds` (de modo que el juego se comporta igual independientemente de la velocidad de fotogramas).

Si el usuario toca el tercer centro de la pantalla, la cámara avanzará hacia delante. Esto se logra primero obteniendo el vector hacia delante, que se define inicialmente como apuntando hacia el eje Y negativo, y girado por una matriz creada con `Matrix.CreateRotationZ` y el valor de `angle`. Por último, el `forwardVector` se aplica a `position` mediante el coeficiente de `unitsPerSecond`.

## <a name="summary"></a>Resumen

En este tutorial se explica cómo moverse y girar `Models` en el espacio 3D mediante `Matrices` y la propiedad `BasicEffect.World`. Esta forma de movimiento proporciona la base para mover objetos en juegos 3D. En este tutorial también se explica cómo implementar una entidad `Camera` para ver el mundo desde cualquier posición y ángulo.

## <a name="related-links"></a>Vínculos relacionados

- [Vínculo de API de monogame](http://www.monogame.net/documentation/?page=api)
- [Proyecto terminado (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
