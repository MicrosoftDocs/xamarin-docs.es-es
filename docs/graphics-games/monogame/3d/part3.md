---
title: Coordenadas 3D en monogame
description: Entender el sistema de coordenadas 3D es un paso importante para desarrollar juegos 3D. Monogame proporciona una serie de clases para colocar, orientar y escalar objetos en el espacio 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: f2e65439084d4e89ae529bda79c2cb2739f1529d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656212"
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

![](part3-images/image1.gif "Una vez finalizada, la aplicación incluirá un proyecto con un robot moviéndose en un círculo y una cámara que se puede controlar mediante la entrada táctil.")


## <a name="creating-a-project"></a>Crear un proyecto

Este tutorial se centra en mover objetos en el espacio 3D. Comenzaremos con el proyecto para representar modelos y matrices de vértices [que se pueden encontrar aquí](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). Una vez descargado, descomprima y abra el proyecto para asegurarse de que se ejecuta y debemos ver lo siguiente:

![](part3-images/image2.png "Una vez descargado, descomprima y abra el proyecto para asegurarse de que se ejecuta y se debe mostrar esta vista.")


## <a name="creating-a-robot-entity"></a>Creación de una entidad robot

Antes de empezar a mover el robot, crearemos una clase `Robot` para que contenga la lógica de dibujo y movimiento. Los desarrolladores de juegos hacen referencia a esta encapsulación de lógica y datos como una *entidad*.

Agregue un nuevo archivo de clase vacía a la biblioteca de clases portable **MonoGame3D** (no a ModelAndVerts. Android específico de la plataforma). Asígnele el nombre **robot** y haga clic en **nuevo**:

![](part3-images/image3.png "Asígnele el nombre robot y haga clic en nuevo.")

Modifique la `Robot` clase de la siguiente manera:

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

El `Robot` código es esencialmente el mismo código en `Game1` para dibujar un `Model`. Para ver una revisión `Model` de la carga y el dibujo, vea [esta guía sobre cómo trabajar con modelos](~/graphics-games/monogame/3d/part1.md). Ahora podemos quitar todo el `Model` código de carga y representación de `Game1`y reemplazarlo por una `Robot` instancia de:

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

![](part3-images/image4.png "Si el código se ejecuta ahora, la aplicación mostrará una escena con un solo robot que se dibuja principalmente en el suelo.")

## <a name="moving-the-robot"></a>Movimiento del robot

Ahora que tenemos una `Robot` clase, podemos agregar lógica de movimiento al robot. En este caso, simplemente haremos que el robot se mueva en un círculo según el tiempo de juego. Se trata de una implementación algo práctica para un juego real, ya que un carácter normalmente responde a la entrada o a la inteligencia artificial, pero proporciona un entorno para que podamos explorar el posicionamiento y la rotación 3D.

La única información que vamos a necesitar desde fuera de `Robot` la clase es el tiempo de juego actual. Vamos a agregar un `Update` método que tomará un `GameTime` parámetro. Este `GameTime` parámetro se usará para incrementar una variable de ángulo que se usará para determinar la posición final del robot.

En primer lugar, vamos a agregar el campo de `Robot` ángulo a la `model` clase en el campo:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Ahora podemos incrementar este valor en una `Update` función:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Necesitamos asegurarse de que se llama al `Update` método desde: `Game1.Update`

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Por supuesto, en este momento el campo de ángulo no hace nada, es necesario escribir código para usarlo. Modificaremos el `Draw` método para que podamos calcular el mundo `Matrix` en un método dedicado: 

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

A continuación, implementaremos el `GetWorldMatrix` método en la `Robot` clase:

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

![](part3-images/image5.gif "La ejecución de este código hace que el robot se mueva en un círculo")

## <a name="matrix-multiplication"></a>Multiplicación de matrices

El código anterior gira el robot mediante la creación de `Matrix` un en `GetWorldMatrix` el método. El `Matrix` struct contiene 16 valores de punto flotante que se pueden usar para traducir (establecer posición), girar y escalar (tamaño establecido). Cuando se asigna la `effect.World` propiedad, se indica al sistema de representación subyacente cómo colocar, cambiar el tamaño y orientar todo lo que se va a dibujar `Model` (o geometría de vértices). 

Afortunadamente, el `Matrix` struct incluye una serie de métodos que simplifican la creación de tipos comunes de matrices. La primera utilizada en el código anterior es `Matrix.CreateTranslation`. La *conversión* de términos matemáticos hace referencia a una operación que da como resultado un punto (o en nuestro caso un modelo) que se mueve de una ubicación a otra sin ninguna otra modificación (como girar o cambiar el tamaño). La función toma un valor X, y y Z para la traducción. Si vemos nuestra escena de arriba abajo, nuestro `CreateTranslation` método (en aislamiento) realiza lo siguiente:

![](part3-images/image6.png "El método CreateTranslation en el aislamiento realiza esta acción.")

La segunda matriz que se crea es una matriz de rotación mediante `CreateRotationZ` la matriz. Este es uno de los tres métodos que se pueden usar para crear la rotación:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Cada método crea una matriz de rotación girando sobre un eje determinado. En nuestro caso, estamos rotando sobre el eje Z, que señala "hacia arriba". Lo siguiente puede ayudar a visualizar cómo funciona la rotación basada en ejes:

![](part3-images/image7.png "Esto puede ayudar a visualizar cómo funciona la rotación basada en ejes")

También usamos el `CreateRotationZ` método con el campo de ángulo, que se incrementa a lo largo del tiempo debido `Update` a la llamada al método. El resultado es que el `CreateRotationZ` método hace que nuestro robot gire alrededor del origen a medida que pasa el tiempo.

La última línea de código combina las dos matrices en una:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Esto se conoce como multiplicación de matrices, que funciona ligeramente diferente de la multiplicación normal. La *propiedad conmutable de* la multiplicación indica que el orden de los números en una operación de multiplicación no cambia el resultado. Es decir, 3 * 4 es equivalente a 4 * 3. La multiplicación de matrices difiere en que no es conmutativa. Es decir, la línea anterior se puede leer como "aplicar translationMatrix para trasladar el modelo y, a continuación, girar todo aplicando rotationMatrix". Podríamos visualizar el modo en que la línea anterior afecta a la posición y la rotación de la manera siguiente:

![](part3-images/image8.png "Una visualización PF el modo en que la línea anterior afecta a la posición y la rotación")

Para ayudar a comprender cómo el orden de multiplicación de la matriz puede afectar al resultado, tenga en cuenta lo siguiente, donde se invierte la multiplicación de la matriz:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

El código anterior rotaría primero el modelo en contexto y, a continuación, lo traduciría:

![](part3-images/image9.png "El código anterior rotaría primero el modelo en contexto y, a continuación, lo traduciría")

Si ejecutamos el código con la multiplicación invertida, observamos que, puesto que el giro se aplica primero, solo afecta a la orientación del modelo y la posición del modelo sigue siendo la misma. En otras palabras, el modelo gira en su lugar:

![](part3-images/image10.gif "El modelo gira en su lugar")

## <a name="creating-the-camera-entity"></a>Creación de la entidad Camera

La `Camera` entidad contendrá toda la lógica necesaria para realizar el movimiento basado en entrada y proporcionar propiedades para asignar propiedades en la `BasicEffect` clase.

En primer lugar, se implementará una cámara estática (sin movimiento basado en entrada) y se integrará en nuestro proyecto existente. Agregue una nueva clase a la biblioteca de clases portable **MonoGame3D** (el mismo proyecto `Robot.cs`con) y asígnele el nombre **Camera**. Reemplace el contenido del archivo por el código siguiente:

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

El código anterior es muy similar al código de `Game1` y `Robot` en `BasicEffect`el que se asignan las matrices. 

Ahora podemos integrar la nueva `Camera` clase en nuestros proyectos existentes. En primer lugar, modificaremos `Robot` la clase para tomar `Camera` una instancia en `Draw` su método, lo que eliminará una gran cantidad de código duplicado. Reemplace el `Robot.Draw` método por lo siguiente:

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

A continuación, modifique `Game1.cs` el archivo:

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

Las modificaciones realizadas en `Game1` de la versión anterior (que se identifican `// New camera code` con) son:

- `Camera`campo de`Game1`
- `Camera`creación de instancias en`Game1.Initialize`
- `Camera.Update`llamar a en`Game1.Update`
- `Robot.Draw`Ahora toma un `Camera` parámetro
- `Game1.Draw`ahora usa `Camera.ViewMatrix` y`Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Mover la cámara con entrada

Hasta ahora, hemos agregado una entidad `Camera` pero no hemos hecho nada con ella para cambiar el comportamiento de tiempo de ejecución. Agregaremos el comportamiento que permite al usuario:

- Toque el lado izquierdo de la pantalla para girar la cámara hacia la izquierda
- Toque el lado derecho de la pantalla para convertir la cámara a la derecha
- Toque el centro de la pantalla para avanzar la cámara

### <a name="making-lookat-relative"></a>Conversión de lookAt en relativa

En primer lugar, actualizaremos la `Camera` clase para incluir un `angle` campo que se usará para establecer la dirección a `Camera` la que se enfrenta. Actualmente, nuestro `Camera` determina la dirección a la que se enfrenta a `lookAtVector`través del local, que `Vector3.Zero`se asigna a. En otras palabras, `Camera` siempre examinamos el origen. Si se mueve la cámara, también cambiará el ángulo al que está orientada la cámara:

![](part3-images/image11.gif "Si la cámara se mueve, también cambiará el ángulo al que está orientada la cámara.")

Queremos que el `Camera` se enfrente a la misma dirección, independientemente de su posición, al menos hasta que implementemos la lógica para `Camera` rotar con la entrada. El primer cambio será ajustar la variable para `lookAtVector` que se base en la ubicación actual, en lugar de buscar una posición absoluta:

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

Esto da como resultado `Camera` la visualización del mundo directo. Observe que el valor `position` inicial se ha modificado para `(0, 20, 10)` que el `Camera` quede centrado en el eje X. La ejecución del juego muestra:

![](part3-images/image12.png "Al ejecutar el juego se muestra esta vista")

### <a name="creating-an-angle-variable"></a>Crear una variable de ángulo

La `lookAtVector` variable controla el ángulo que está viendo nuestra cámara. Actualmente se corrige para ver el eje y negativo y ligeramente inclinado hacia abajo (desde el `-.5f` valor Z). Vamos a crear una `angle` variable que se usará para ajustar la `lookAtVector` propiedad. 

En las secciones anteriores de este tutorial, mostramos que las matrices se pueden usar para girar la forma en que se dibujan los objetos. También podemos usar matrices para girar vectores como `lookAtVector` con el `Vector3.Transform` método. 

Agregue un `angle` campo y modifique la `ViewMatrix` propiedad como se indica a continuación:

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

Nuestra `Camera` entidad ahora se puede controlar por completo a través de sus variables de posición y ángulo, solo tenemos que cambiarlas de acuerdo con la entrada.

En primer lugar, obteneremos `TouchPanel` el estado para buscar el lugar en el que el usuario toca la pantalla. Para obtener más información sobre el `TouchPanel` uso de la clase, vea [la referencia de la API de Touchpanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Si el usuario está tocando a la izquierda en tercer lugar, ajustaremos `angle` el valor `Camera` para que gire a la izquierda y, si el usuario está tocando el tercero de la derecha, giraremos de otro modo. Si el usuario está tocando en el tercio medio de la pantalla, se moverá `Camera` hacia delante.

En primer lugar, agregue una instrucción using para `TouchPanel` calificar las `Camera.cs`clases y `TouchCollection` en:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

A continuación, modifique `Update` el método para leer el panel táctil y ajuste `angle` las `position` variables y adecuadamente:

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

Ahora, `Camera` responderá a la entrada táctil:

![](part3-images/image1.gif "Ahora la cámara responderá a la entrada táctil")

El método Update comienza llamando a `TouchPanel.GetState`, que devuelve una colección de toques. Aunque `TouchPanel.GetState` puede devolver varios puntos táctiles, solo nos preocuparemos por la simplicidad.

Si el usuario toca la pantalla, el código comprueba si la primera entrada está en el tercio izquierdo, medio o derecho de la pantalla. Los tercios izquierdo y derecho giran la cámara aumentando o disminuyendo la `angle` variable según el `TotalSeconds` valor (de modo que el juego se comporta igual independientemente de la velocidad de fotogramas).

Si el usuario toca el tercer centro de la pantalla, la cámara avanzará hacia delante. Esto se logra primero obteniendo el vector hacia delante, que se define inicialmente como apuntando hacia el eje Y negativo y, a continuación, girado por `Matrix.CreateRotationZ` una matriz `angle` creada con y el valor. Finalmente, `position` se aplica a utilizando el `unitsPerSecond` coeficiente. `forwardVector`

## <a name="summary"></a>Resumen

En este tutorial se explica cómo moverse y `Models` girar en el espacio `Matrices` 3D mediante `BasicEffect.World` y la propiedad. Esta forma de movimiento proporciona la base para mover objetos en juegos 3D. En este tutorial también se explica cómo implementar `Camera` una entidad para ver el mundo a partir de cualquier posición y ángulo.

## <a name="related-links"></a>Vínculos relacionados

- [Vínculo de API de monogame](http://www.monogame.net/documentation/?page=api)
- [Proyecto terminado (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
