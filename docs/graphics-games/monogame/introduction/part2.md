---
title: 'Parte 2: implementación de WalkingGame'
description: En este tutorial se muestra cómo agregar la lógica de juego y el contenido a un proyecto de monogame vacío para crear una demostración de un Sprite animado que se mueve con entrada táctil.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 835beca6866f5d3f82ae48d1c99557ceafe360ba
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429772"
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2: implementación de WalkingGame

_En este tutorial se muestra cómo agregar la lógica de juego y el contenido a un proyecto de monogame vacío para crear una demostración de un Sprite animado que se mueve con entrada táctil._

En las partes anteriores de este tutorial se ha mostrado cómo crear proyectos vacíos monogame. Crearemos estas partes anteriores creando una demostración de juego sencilla. Este artículo contiene las siguientes secciones:

- Descomprimir el contenido del juego
- Información general de la clase monogame
- Representar nuestro primer Sprite
- Crear CharacterEntity
- Agregar CharacterEntity al juego
- Crear la clase Animation
- Agregar la primera animación a CharacterEntity
- Agregar movimiento al carácter
- Movimiento y animación coincidentes

## <a name="unzipping-our-game-content"></a>Descomprimir el contenido del juego

Antes de empezar a escribir código, queremos descomprimir el *contenido*del juego. Los desarrolladores de juegos suelen usar el término *contenido* para hacer referencia a archivos que no son de código, que normalmente se crean mediante artistas visuales, diseñadores de juegos o diseñadores de audio. Entre los tipos comunes de contenido se incluyen los archivos que se usan para mostrar objetos visuales, reproducir sonido o controlar el comportamiento de inteligencia artificial (AI). El contenido de la perspectiva del equipo de desarrollo de juegos normalmente lo crean los programadores que no son de.

El contenido que se usa aquí se puede encontrar [en github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Necesitaremos estos archivos descargados en una ubicación a la que se vaya a acceder más adelante en este tutorial.

## <a name="monogame-class-overview"></a>Información general de la clase monogame

En el caso de los iniciadores, exploraremos las clases de monogame usadas en la representación básica:

- `SpriteBatch` : se usa para dibujar gráficos 2D en la pantalla. Los *sprites* son elementos visuales 2D que se usan para mostrar imágenes en la pantalla. El `SpriteBatch` objeto puede dibujar un solo Sprite cada vez entre sus `Begin` `End` métodos y, o bien se pueden agrupar varios sprites, o *procesarlos por lotes*.
- `Texture2D` : representa un objeto de imagen en tiempo de ejecución. `Texture2D` las instancias se crean a menudo a partir de formatos de archivo como. png o. bmp, aunque también se pueden crear dinámicamente en tiempo de ejecución. `Texture2D` las instancias de se utilizan al representar con `SpriteBatch` instancias de.
- `Vector2` : representa una posición en un sistema de coordenadas 2D que se suele usar para colocar objetos visuales. Monogame también incluye `Vector3` y `Vector4` , pero solo usaremos `Vector2` en este tutorial.
- `Rectangle` : un área de cuatro lados con la posición, el ancho y el alto. Vamos a usar esto para definir la parte de que se `Texture2D` va a representar al empezar a trabajar con animaciones.

También debemos tener en cuenta que Microsoft no mantiene monogame, a pesar de su espacio de nombres. El `Microsoft.Xna` espacio de nombres se usa en monogame para facilitar la migración de proyectos de XNA existentes a monogame.

## <a name="rendering-our-first-sprite"></a>Representar nuestro primer Sprite

A continuación, dibujaremos un solo Sprite en la pantalla para mostrar cómo realizar la representación en 2D en monogame.

### <a name="creating-a-texture2d"></a>Creación de un Texture2D

Es necesario crear una `Texture2D` instancia de que se usará al representar el sprite. Todo el contenido del juego se incluye en última instancia en una carpeta denominada **Content,** que se encuentra en el proyecto específico de la plataforma. Los proyectos compartidos monogame no pueden contener contenido, ya que el contenido debe usar acciones de compilación específicas de la plataforma. La carpeta de contenido se puede encontrar en el proyecto de iOS y dentro de la carpeta assets del proyecto de Android.

Para agregar el contenido del juego, haga clic con el botón derecho en la carpeta de **contenido** y seleccione **Agregar > agregar archivos..** . Navegue hasta la ubicación donde se extrajo el archivo de content.zip y seleccione el archivo de **charactersheet.png** . Si se le pregunta cómo agregar el archivo a la carpeta, debemos seleccionar la opción de **copia** :

![Si se le pregunta cómo agregar el archivo a la carpeta, seleccione la opción copiar.](part2-images/image1.png)

La carpeta de contenido contiene ahora el archivo charactersheet.png:

![La carpeta de contenido contiene ahora el archivo de charactersheet.png](part2-images/image2.png)

A continuación, agregaremos código para cargar el archivo de charactersheet.png y crear un `Texture2D` . Para ello, abra el `Game1.cs` archivo y agregue el siguiente campo a la clase Game1.CS:

```csharp
Texture2D characterSheetTexture;
```

A continuación, crearemos `characterSheetTexture` en el `LoadContent` método. Antes de que cualquier método de modificación tenga `LoadContent` el siguiente aspecto:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Debemos tener en cuenta que el proyecto predeterminado ya crea instancias `spriteBatch` de la instancia para nosotros. Vamos a usarlo más adelante, pero no vamos a agregar código adicional para preparar el `spriteBatch` para su uso. Por otro lado, nuestra `spriteSheetTexture` requiere inicialización, por lo que la inicializaremos una vez que `spriteBatch` se haya creado:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Ahora que tenemos una `SpriteBatch` instancia de y una `Texture2D` instancia de, podemos agregar nuestro código de representación al `Game1.Draw` método:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

La ejecución del juego muestra ahora un solo Sprite que muestra la textura creada a partir de charactersheet.png:

![La ejecución del juego muestra ahora un solo Sprite que muestra la textura creada a partir de charactersheet.png](part2-images/image3.png)

## <a name="creating-the-characterentity"></a>Crear CharacterEntity

Hasta ahora hemos agregado código para representar un solo Sprite en la pantalla. sin embargo, si vamos a desarrollar un juego completo sin crear ninguna otra clase, ejecutaremos problemas con la organización de código.

### <a name="what-is-an-entity"></a>¿Qué es una entidad?

Un patrón común para organizar el código de juego es crear una nueva clase para cada tipo de *entidad* de juego. Una entidad en el desarrollo de juegos es un objeto que puede contener algunas de las siguientes características (no se requieren todas):

- Elemento visual como un Sprite, texto o modelo 3D
- Física o de cada fotograma, como una unidad que patrulla una ruta de acceso set o un carácter de jugador que responde a la entrada
- Se puede crear y destruir de forma dinámica, como una aparición y un recolector del reproductor.

Los sistemas de organización de entidades pueden ser complejos y muchos motores de juegos ofrecen clases para ayudar a administrar las entidades. Vamos a implementar un sistema de entidades muy simple, por lo que merece la pena mencionar que los juegos completos suelen requerir más organización en la parte del desarrollador.

### <a name="defining-the-characterentity"></a>Definir CharacterEntity

Nuestra entidad, a la que llamaremos `CharacterEntity` , tendrá las siguientes características:

- La capacidad de cargar su propia `Texture2D`
- La capacidad de representarse a sí misma, incluida la inclusión de métodos de llamada para actualizar la animación de caminar.
- `X` y las propiedades y para controlar la posición del carácter.
- La capacidad de actualizarse, en concreto, para leer los valores de la pantalla táctil y ajustar la posición de manera adecuada.

Para agregar `CharacterEntity` al juego, haga clic con el botón derecho o haga clic en el control en el proyecto **WalkingGame** y seleccione **Agregar > nuevo archivo..**.. Seleccione la opción **clase vacía** y asigne al nuevo archivo el nombre **CharacterEntity**y, a continuación, haga clic en **nuevo**.

En primer lugar, agregaremos la capacidad de `CharacterEntity` para que cargue un `Texture2D` , así como para dibujarse a sí mismo. Se modificará el archivo recién agregado de la `CharacterEntity.cs` siguiente manera:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;

namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

El código anterior agrega la responsabilidad de colocar, representar y cargar el contenido en `CharacterEntity` . Dedique un momento a señalar algunas consideraciones tomadas en el código anterior.

### <a name="why-are-x-and-y-floats"></a>¿Por qué son los valores Float X e y?

Los desarrolladores que no están familiarizados con la programación de juegos pueden preguntarse por qué `float` se usa el tipo en lugar de `int` o `double` . La razón es que un valor de 32 bits es más común para el posicionamiento en código de representación de bajo nivel. El tipo Double ocupa 64 bits para la precisión, lo que rara vez es necesario para colocar objetos. Aunque una diferencia de 32 bits puede parecer insignificante, muchos juegos modernos incluyen gráficos que requieren el procesamiento de decenas de miles de valores de posición cada fotograma (30 o 60 veces por segundo). La reducción de la cantidad de memoria que debe desplazarse a través de la canalización de gráficos a la mitad puede tener un impacto significativo en el rendimiento de un juego.

El `int` tipo de datos puede ser una unidad de medida adecuada para la posición, pero puede colocar limitaciones en la forma en que se colocan las entidades. Por ejemplo, el uso de valores enteros hace que sea más difícil implementar el movimiento suave para juegos que admiten el zoom o las cámaras 3D (que permiten `SpriteBatch` ).

Por último, veremos que la lógica que mueve el carácter alrededor de la pantalla lo hará con los valores de tiempo del juego. El resultado de multiplicar la velocidad por la cantidad de tiempo que ha pasado en un fotograma determinado rara vez producirá un número entero, por lo que es necesario usar `float` para `X` y `Y` .

### <a name="why-is-charactersheettexture-static"></a>¿Por qué characterSheetTexture es estático?

La `characterSheetTexture` `Texture2D` instancia es una representación en tiempo de ejecución del archivo de charactersheet.png. En otras palabras, contiene los valores de color de cada píxel que se extraen del archivo de **charactersheet.png** de origen. Si el juego creara dos `CharacterEntity` instancias, cada una necesitaría acceder a la información de charactersheet.png. En esta situación, no se querrá incurrir en el costo de rendimiento de la carga charactersheet.png dos veces, ni sería necesario tener memoria de textura duplicada almacenada en RAM. El uso de un `static` campo nos permite compartir el mismo `Texture2D` en todas las `CharacterEntity` instancias.

`static`El uso de miembros para la representación en tiempo de ejecución del contenido es una solución simplista y no soluciona los problemas que se producen en juegos más grandes, como la descarga de contenido al pasar de un nivel a otro. Las soluciones más sofisticadas, que van más allá del ámbito de este tutorial, incluyen el uso de la canalización de contenido de monogame o la creación de un sistema de administración de contenido personalizado.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>¿Por qué se pasa SpriteBatch como argumento en lugar de crear instancias de la entidad?

El `Draw` método tal y como se ha implementado toma un `SpriteBatch` argumento, pero por `characterSheetTexture` el contrario se crea una instancia del `CharacterEntity` .

La razón es que la representación más eficaz es posible cuando se utiliza la misma `SpriteBatch` instancia para todas las `Draw` llamadas y cuando todas las `Draw` llamadas se realizan entre un único conjunto de `Begin` `End` llamadas y. Por supuesto, el juego solo incluirá una instancia de una sola entidad, pero los juegos más complicados se beneficiarán del patrón que permite que varias entidades usen la misma `SpriteBatch` instancia.

## <a name="adding-characterentity-to-the-game"></a>Agregar CharacterEntity al juego

Ahora que hemos agregado nuestro `CharacterEntity` código para su representación, podemos reemplazar el código en `Game1.cs` para usar una instancia de esta nueva entidad. Para ello, reemplazaremos el `Texture2D` campo por un `CharacterEntity` campo en `Game1` :

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

A continuación, agregaremos la creación de instancias de esta entidad en `Game1.Initialize` :

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

También es necesario quitar la `Texture2D` creación de `LoadContent` , ya que ahora se controla dentro de nuestro `CharacterEntity` . `Game1.LoadContent` debería tener este aspecto:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Merece la pena tener en cuenta que, a pesar de su nombre `LoadContent` , el método no es el único lugar donde se puede cargar el contenido: muchos juegos implementan la carga de contenido en su método Initialize, o incluso en su código de actualización como contenido, puede que no sea necesario hasta que el jugador llegue a un punto determinado del juego.

Por último, podemos modificar el método draw de la manera siguiente:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Si ejecutamos el juego, ahora veremos el carácter. Puesto que X e y tienen como valor predeterminado 0, el carácter se coloca en la esquina superior izquierda de la pantalla:

![Puesto que X e y tienen como valor predeterminado 0, el carácter se coloca en la esquina superior izquierda de la pantalla.](part2-images/image4.png)

## <a name="creating-the-animation-class"></a>Crear la clase Animation

Actualmente, se `CharacterEntity` muestra el archivo de **charactersheet.png** completo. Esta disposición de varias imágenes en un archivo se conoce como una *hoja de Sprite*. Normalmente, un Sprite solo representará una parte de la hoja de Sprite. Modificaremos el `CharacterEntity` para representar una parte de esta **charactersheet.png**y esta parte cambiará con el tiempo para mostrar una animación de caminar.

Crearemos la `Animation` clase para controlar la lógica y el estado de la animación CharacterEntity. La clase Animation será una clase general que podría usarse para cualquier entidad, no solo para `CharacterEntity` animaciones. Ultimate la `Animation` clase proporcionará el `Rectangle` que `CharacterEntity` utilizará al dibujarse a sí mismo. También crearemos una `AnimationFrame` clase que se utilizará para definir la animación.

### <a name="defining-animationframe"></a>Definir AnimationFrame

`AnimationFrame` no contendrá ninguna lógica relacionada con la animación. Solo lo usaremos para almacenar los datos. Para agregar la `AnimationFrame` clase, haga clic con el botón derecho o haga clic en el control en el proyecto compartido **WalkingGame** y seleccione **Agregar > nuevo archivo....** Escriba el nombre **AnimationFrame** y haga clic en el botón **nuevo** . Vamos a modificar el `AnimationFrame.cs` archivo para que contenga el código siguiente:

```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

La `AnimationFrame` clase contiene dos fragmentos de información:

- `SourceRectangle` : Define el área del `Texture2D` que mostrará el `AnimationFrame` . Este valor se mide en píxeles, donde la parte superior izquierda es (0,0).
- `Duration` : Define cuánto tiempo `AnimationFrame` se muestra cuando se usa en un `Animation` .

### <a name="defining-animation"></a>Definir animación

La `Animation` clase contendrá una `List<AnimationFrame>` , así como la lógica para cambiar el marco que se muestra actualmente según la cantidad de tiempo transcurrido.

Para agregar la `Animation` clase, haga clic con el botón derecho o haga clic en el control en el proyecto compartido **WalkingGame** y seleccione **Agregar > nuevo archivo..**.. Escriba la **animación** de nombre y haga clic en el botón **nuevo** . Vamos a modificar el `Animation.cs` archivo para que contenga el código siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;

            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Echemos un vistazo a algunos de los detalles de la `Animation` clase.

### <a name="frames-list"></a>Lista de marcos

El `frames` miembro es lo que almacena los datos de la animación. El código que crea instancias de las animaciones agregará `AnimationFrame` instancias a la `frames` lista a través del `AddFrame` método. Una implementación más completa puede ofrecer `public` métodos o propiedades para modificar `frames` , pero limitaremos la funcionalidad a agregar fotogramas para este tutorial.

### <a name="duration"></a>Duration

Duration devuelve la duración total del `Animation,` que se obtiene al agregar la duración de todas las instancias contenidas `AnimationFrame` . Este valor se puede almacenar en caché si se trata de `AnimationFrame` un objeto inmutable, pero como se implementó AnimationFrame como una clase que se puede cambiar después de agregarse a la animación, es necesario calcular este valor cada vez que se tiene acceso a la propiedad.

### <a name="update"></a>Actualizar

Se `Update` debe llamar al método cada fotograma (es decir, cada vez que se actualiza todo el juego). Su finalidad es aumentar el `timeIntoAnimation` miembro que se usa para devolver el fotograma que se muestra actualmente. La lógica de `Update` evita que el `timeIntoAnimation` de sea más grande que la duración de la animación completa.

Como vamos a usar la `Animation` clase para mostrar una animación de caminar, queremos que se repita esta animación cuando se alcance el final. Para ello, se comprueba si `timeIntoAnimation` es mayor que el `Duration` valor. Si es así, se devolverá al principio y se conservará el resto, lo que dará lugar a una animación de bucle.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtener el rectángulo del marco actual

En última instancia, el propósito de la `Animation` clase es proporcionar un `Rectangle` objeto que se puede utilizar al dibujar un Sprite. Actualmente `Animation` , la clase contiene la lógica para cambiar el `timeIntoAnimation` miembro, que se usará para obtener que `Rectangle` se debe mostrar. Haremos esto creando una `CurrentRectangle` propiedad en la `Animation` clase. Copie esta propiedad en la `Animation` clase:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Agregar la primera animación a CharacterEntity

Contendrá `CharacterEntity` animaciones para el recorrido y la posición, así como una referencia al actual `Animation` que se muestra.

En primer lugar, agregaremos lo primero `Animation,` que usaremos para probar la funcionalidad tal como está escrita hasta ahora. Vamos a agregar los siguientes miembros a la clase CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

A continuación, vamos a definir la `walkDown` animación. Para ello, modifique el `CharacterEntity` constructor de la siguiente manera:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Como se mencionó anteriormente, es necesario llamar a `Animation.Update` para que se reproduzcan animaciones basadas en el tiempo. También necesitamos asignar el `currentAnimation` . Por ahora, asignaremos `currentAnimation` a `walkDown` , pero vamos a reemplazar este código más adelante cuando implementemos nuestra lógica de movimiento. Vamos a agregar el `Update` método a `CharacterEntity` como se indica a continuación:

```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ahora que tenemos `currentAnimation` asignado y actualizado, podemos usarlo para realizar el dibujo. Modificaremos `CharacterEntity.Draw` de la siguiente manera:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

El último paso para obtener la `CharacterEntity` animación es llamar al método recién agregado `Update` desde `Game1` . Modifique `Game1.Update` como se indica a continuación:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Ahora el `CharacterEntity` reproducirá su `walkDown` animación:

![Ahora, el CharacterEntity reproducirá su animación walkDown](part2-images/image5.gif)

## <a name="adding-movement-to-the-character"></a>Agregar movimiento al carácter

A continuación, agregaremos movimiento a nuestro carácter con los controles táctiles. Cuando el usuario toca la pantalla, el carácter se desplaza hacia el punto en el que se toca la pantalla. Si no se detectan toques, el carácter se pondrá en marcha.

### <a name="defining-getdesiredvelocityfrominput"></a>Definir GetDesiredVelocityFromInput

Vamos a usar la clase de monogame `TouchPanel` , que proporciona información sobre el estado actual de la pantalla táctil. Vamos a agregar un método que comprobará `TouchPanel` y devolverá la velocidad deseada del carácter:

```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

El `TouchPanel.GetState` método devuelve un `TouchCollection` que contiene información sobre el lugar en el que el usuario toca la pantalla. Si el usuario no toca la pantalla, el `TouchCollection` estará vacío, en cuyo caso no se debe cambiar el carácter.

Si el usuario toca la pantalla, se moverá el carácter hacia el primer toque, en otras palabras, `TouchLocation` en el índice 0. Inicialmente, estableceremos la velocidad deseada para que sea igual a la diferencia entre la ubicación del carácter y la ubicación del primer toque:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Lo que se muestra a continuación es un poco de matemáticas que evitará que el carácter se mueva a la misma velocidad. Para ayudar a explicar por qué esto es importante, consideremos una situación en la que el usuario toca la pantalla 500 píxeles fuera del lugar donde se encuentra el carácter. La primera línea en la que `desiredVelocity.X` se establece asignaría un valor de 500. Sin embargo, si el usuario estuviera tocando la pantalla a una distancia de solo 100 unidades del carácter, `desiredVelocity.X` se establecería en 100. El resultado sería que la velocidad de movimiento del carácter respondiera hasta el punto en que el punto táctil proviene del carácter. Como queremos que el carácter se mueva siempre a la misma velocidad, es necesario modificar el desiredVelocity.

La `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instrucción está comprobando si la velocidad es distinta de cero; en otras palabras, se está comprobando para asegurarse de que el usuario no toca el mismo lugar que la posición actual del carácter. Si no es así, debemos establecer la velocidad del carácter en constante, independientemente de lo lejos que esté el toque. Para ello, se normaliza el vector de velocidad, lo que da como resultado una longitud de 1. Un vector de velocidad de 1 significa que el carácter se moverá a 1 píxel por segundo. Aceleraremos este paso multiplicando el valor por la velocidad deseada de 200.

### <a name="applying-velocity-to-position"></a>Aplicando la velocidad a la posición

La velocidad devuelta por `GetDesiredVelocityFromInput` debe aplicarse a los `X` valores y del carácter para que `Y` surtan efecto en tiempo de ejecución. Modificaremos el `Update` método de la siguiente manera:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Lo que hemos implementado aquí se denomina movimiento *basado en el tiempo* (en oposición al movimiento *basado en fotogramas* ). El movimiento basado en el tiempo multiplica un valor de velocidad (en nuestro caso, los valores almacenados en la `velocity` variable) por la cantidad de tiempo transcurrido desde la última actualización que se almacena en `gameTime.ElapsedGameTime.TotalSeconds` . Si el juego se ejecuta en menos fotogramas por segundo, el tiempo transcurrido entre fotogramas aumenta: el resultado final es que los objetos que usan el movimiento basado en el tiempo siempre se moverán a la misma velocidad, independientemente de la velocidad de fotogramas.

Si ejecutamos el juego ahora, veremos que el carácter se desplaza hacia la ubicación táctil:

![El carácter se desplaza hacia la ubicación táctil](part2-images/image6.gif)

## <a name="matching-movement-and-animation"></a>Movimiento y animación coincidentes

Una vez que tenemos nuestro carácter moviéndose y jugando a una sola animación, podemos definir el resto de las animaciones y, después, usarlas para reflejar el movimiento del carácter. Cuando termine, tendremos ocho animaciones en total:

- Animaciones para caminar hacia arriba, hacia abajo, a la izquierda y a la derecha
- Animaciones para permanecer hacia arriba, hacia abajo, a la izquierda y a la derecha

### <a name="defining-the-rest-of-the-animations"></a>Definir el resto de las animaciones

Primero agregaremos las `Animation` instancias a la `CharacterEntity` clase para todas las animaciones en el mismo lugar donde se agregaron `walkDown` . Una vez hecho esto, el `CharacterEntity` tendrá los siguientes `Animation` miembros:

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Ahora definiremos las animaciones en el constructor de la `CharacterEntity` siguiente manera:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Debemos tener en cuenta que el código anterior se ha agregado al `CharacterEntity` constructor para que este tutorial sea más corto. Normalmente, los juegos separan la definición de animaciones de caracteres en sus propias clases o cargan esta información a partir de un formato de datos como XML o JSON.

A continuación, ajustaremos la lógica para usar las animaciones en función de la dirección en la que se mueve el carácter o de acuerdo con la última animación si el carácter se acaba de detener. Para ello, modificaremos el `Update` método:

```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

El código para cambiar las animaciones se divide en dos bloques. El primero comprueba si la velocidad no es igual a `Vector2.Zero` , lo que indica si el carácter se está moviendo. Si el carácter se mueve, podemos ver los `velocity.X` `velocity.Y` valores y para determinar la animación de caminar que se va a reproducir.

Si el carácter no se mueve, queremos establecer el carácter `currentAnimation` en una animación permanente, pero solo lo hacemos si `currentAnimation` es una animación de caminar o si no se ha establecido una animación. Si `currentAnimation` no es una de las cuatro animaciones de recorrido, el carácter ya está pendiente, por lo que no es necesario cambiar `currentAnimation` .

El resultado de este código es que el carácter se animará correctamente al caminar y, a continuación, se enfrente a la última dirección en la que se encaminar cuando se detiene:

![El resultado de este código es que el carácter se animará correctamente al caminar y, a continuación, se enfrente a la última dirección en la que estaba caminar cuando se detiene.](part2-images/image7.gif)

## <a name="summary"></a>Resumen

En este tutorial se ha mostrado cómo trabajar con monogame para crear un juego multiplataforma con sprites, mover objetos, detectar entradas y animaciones. Describe la creación de una clase de animación de uso general. También se ha mostrado cómo crear una entidad de caracteres para organizar la lógica del código.

## <a name="related-links"></a>Vínculos relacionados

- [Recurso de imagen CharacterSheet (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Recorrer juego completo (ejemplo)](/samples/xamarin/mobile-samples/walkinggamemg/)