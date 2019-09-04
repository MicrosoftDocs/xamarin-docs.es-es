---
title: Dibujo de gráficos 3D con vértices en monogame
description: Monogame admite el uso de matrices de vértices para definir cómo se representa un objeto 3D por punto. Los usuarios pueden aprovechar las ventajas de las matrices de vértices para crear geometría dinámica, implementar efectos especiales y mejorar la eficacia de su representación a través de la selección.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8bdef9bff975365172a4c215b21cbb07a37e8492
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227723"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Dibujo de gráficos 3D con vértices en monogame

_Monogame admite el uso de matrices de vértices para definir cómo se representa un objeto 3D por punto. Los usuarios pueden aprovechar las ventajas de las matrices de vértices para crear geometría dinámica, implementar efectos especiales y mejorar la eficacia de su representación a través de la selección._

Los usuarios que hayan leído la [Guía sobre los modelos de representación](~/graphics-games/monogame/3d/part1.md) estarán familiarizados con la representación de un modelo 3D en monogame. La `Model` clase es una manera eficaz de representar gráficos 3D cuando se trabaja con datos definidos en un archivo (como. FBX) y cuando se trabaja con datos estáticos. Algunos juegos requieren geometría 3D para definirse o manipularse dinámicamente en tiempo de ejecución. En estos casos, podemos usar matrices de vértices para definir y representar geometría. Un vértice es un término general para un punto en el espacio 3D que forma parte de una lista ordenada que se usa para definir la geometría. Normalmente, los vértices se ordenan de manera que se define una serie de triángulos.

Para ayudar a visualizar cómo se usan los vértices para crear objetos 3D, veamos la siguiente esfera:

![](part2-images/image1.png "Para ayudar a visualizar cómo se usan los vértices para crear objetos 3D, tenga en cuenta esta esfera")

Como se indicó anteriormente, la esfera se compone claramente de varios triángulos. Podemos ver el modelo de alambres de la esfera para ver cómo se conectan los vértices a los triángulos de forma:

![](part2-images/image2.png "Ver el modelo de alambre de la esfera para ver cómo se conectan los vértices a los triángulos del formulario")

En este tutorial se tratarán los siguientes temas:

- Creación de un proyecto
- Crear los vértices
- Agregar código de dibujo
- Representación con una textura
- Modificar coordenadas de textura
- Representar vértices con modelos

El proyecto terminado contendrá un plano inferior que se dibujará con una matriz de vértices:

![](part2-images/image3.png "El proyecto terminado contendrá un plano inferior que se dibujará con una matriz de vértices.")

## <a name="creating-a-project"></a>Crear un proyecto

En primer lugar, se descargará un proyecto que servirá como punto de partida. Usaremos el proyecto de modelos [que se puede encontrar aquí](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/).

Una vez descargado y descomprimido, abra y ejecute el proyecto. Esperamos ver seis modelos de robot que se dibujan en pantalla:

![](part2-images/image4.png "Seis modelos de robot que se dibujan en pantalla")

Al final de este proyecto, se combinará nuestra propia representación de vértices personalizada con el robot `Model`, por lo que no vamos a eliminar el código de representación del robot. En su lugar, simplemente borraremos la `Game1.Draw` llamada para quitar el dibujo de 6 robots por ahora. Para ello, abra el archivo **Game1.CS** y busque el `Draw` método. Modifíquelo para que contenga el código siguiente:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Esto hará que el juego muestre una pantalla azul vacía:

![](part2-images/image5.png "Esto hará que el juego muestre una pantalla azul vacía")

## <a name="creating-the-vertices"></a>Crear los vértices

Crearemos una matriz de vértices para definir nuestra geometría. En este tutorial, vamos a crear un plano 3D (un cuadrado en el espacio 3D, no un avión). Aunque nuestro plano tiene cuatro lados y cuatro vértices, se compone de dos triángulos, cada uno de los cuales requiere tres vértices. Por lo tanto, se definirá un total de seis puntos.

Hasta ahora hemos hablado de los vértices en sentido general, pero monogame proporciona algunos Structs estándar que se pueden usar para los vértices:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

El nombre de cada tipo indica los componentes que contiene. Por ejemplo, `VertexPositionColor` contiene valores para la posición y el color. Echemos un vistazo a cada uno de los componentes:

- Posición: todos los tipos de vértice `Position` incluyen un componente. Los `Position` valores definen dónde se encuentra el vértice en el espacio 3D (X, y y Z).
- Color: los vértices pueden especificar opcionalmente un valor para realizar un `Color` matiz personalizado.
- Normal: las normales definen el modo de cara de la superficie del objeto. Las normales son necesarias si la representación de un objeto con iluminación desde la dirección en la que se enfrenta una superficie afecta a la cantidad de luz que recibe. Normalmente, las normales se especifican como un *Vector de unidad* : un vector 3D que tiene una longitud de 1.
- Texture: textura hace referencia a las coordenadas de textura, es decir, qué parte de una textura debe aparecer en un vértice determinado. Los valores de textura son necesarios si se representa un objeto 3D con una textura. Las coordenadas de textura son coordenadas normalizadas, lo que significa que los valores se encuentran entre 0 y 1. Veremos las coordenadas de textura con más detalle más adelante en esta guía.

Nuestro plano servirá como planta y querrámos aplicar una textura al realizar la representación, por lo que usaremos el tipo para definir los `VertexPositionTexture` vértices.

En primer lugar, vamos a agregar un miembro a nuestra clase Game1:

```csharp
VertexPositionTexture[] floorVerts;
```

A continuación, defina nuestros vértices en `Game1.Initialize`. Tenga en cuenta que la plantilla proporcionada a la que se hace referencia anteriormente en `Game1.Initialize` este artículo no contiene un método, por lo que necesitamos `Game1`agregar el método completo a:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

Para que le resulte más fácil ver el aspecto que tendrán los vértices, tenga en cuenta el siguiente diagrama:

![](part2-images/image6.png "Para que le resulte más fácil visualizar el aspecto de los vértices, tenga en cuenta este diagrama")

Necesitamos confiar en nuestro diagrama para visualizar los vértices hasta que terminemos de implementar el código de representación.

## <a name="adding-drawing-code"></a>Agregar código de dibujo

Ahora que tenemos las posiciones de la geometría definida, podemos escribir nuestro código de representación.

En primer lugar, es necesario definir una `BasicEffect` instancia de que contendrá los parámetros para la representación, como la posición y la iluminación. Para ello, agregue un `BasicEffect` miembro a la `Game1` clase siguiente, donde se `floorVerts` define el campo:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

A continuación, modifique `Initialize` el método para definir el efecto:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Ahora podemos agregar código para realizar el dibujo:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

Tendremos que llamar `DrawGround` a `Game1.Draw`en:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

La aplicación mostrará lo siguiente cuando se ejecute:

![](part2-images/image7.png "La aplicación mostrará esto cuando se ejecute")

Echemos un vistazo a algunos de los detalles del código anterior.

### <a name="view-and-projection-properties"></a>Propiedades de vista y proyección

Las `View` propiedades `Projection` y controlan cómo se ve la escena. Vamos a modificar este código más adelante cuando se vuelva a agregar el código de representación del modelo. En concreto `View` , controla la ubicación y la orientación de la cámara `Projection` y controla el *campo de la vista* (que se puede utilizar para acercar la cámara).

### <a name="techniques-and-passes"></a>Técnicas y pases

Una vez que se han asignado propiedades en nuestro efecto, podemos realizar la representación real.

No vamos a cambiar la `CurrentTechnique` propiedad en este tutorial, pero los juegos más avanzados pueden tener un solo efecto que puede dibujar de maneras diferentes (por ejemplo, cómo se aplica el valor de color). Cada uno de estos modos de representación se puede representar como una técnica que se puede asignar antes de la representación. Además, cada técnica puede requerir varios pasos para que se representen correctamente. Los efectos pueden necesitar varios pases si se representan objetos visuales complejos, como una superficie o un pelo brillante.

Lo importante es tener en cuenta que el `foreach` bucle permite que el mismo C# código represente cualquier efecto independientemente de la complejidad del subyacente `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives`es donde se representan los vértices. El primer parámetro indica al método cómo hemos organizado nuestros vértices. Se han estructurado de modo que cada triángulo esté definido por tres vértices ordenados, por lo `PrimitiveType.TriangleList` que usamos el valor.

El segundo parámetro es la matriz de vértices que se definió anteriormente.

El tercer parámetro especifica el primer índice que se va a dibujar. Dado que queremos que se represente toda la matriz de vértices, pasaremos un valor de 0.

Por último, se especifica el número de triángulos que se van a representar. Nuestra matriz de vértices contiene dos triángulos, por lo que debe pasar un valor de 2.

## <a name="rendering-with-a-texture"></a>Representación con una textura

En este punto, la aplicación representa un plano blanco (en perspectiva). A continuación, agregaremos una textura al proyecto que se usará al representar nuestro plano.

Para simplificar las cosas, agregaremos el. png directamente a nuestro proyecto en lugar de usar la herramienta de canalización monogame. Para ello, descargue [este archivo. png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) en el equipo. Una vez descargado, haga clic con el botón derecho en la carpeta de **contenido** en el panel de solución y seleccione **Agregar > Agregar archivos..** .. Si trabaja en Android, esta carpeta se encuentra en la carpeta **activos** del proyecto específico de Android. En el caso de iOS, esta carpeta estará en la raíz del proyecto de iOS. Navegue hasta la ubicación donde se guarda el archivo de **cuadros de ajedrez. png** y selecciónelo. Seleccione esta copia para copiar el archivo en el directorio.

A continuación, agregaremos el código para crear `Texture2D` la instancia. En primer lugar, `Texture2D` agregue como miembro de `Game1` en la `BasicEffect` instancia de:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modifique `Game1.LoadContent` como se indica a continuación:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

A continuación, modifique `DrawGround` el método. La única modificación necesaria es `effect.TextureEnabled` asignar a `true` y establecer `effect.Texture` en `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Por último, es necesario modificar el `Game1.Initialize` método para asignar también coordenadas de textura en nuestros vértices:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Si ejecutamos el código, podemos ver que nuestro plano ahora muestra un patrón de tablero de ajedrez:

![](part2-images/image8.png "El plano muestra ahora un patrón de tablero de ajedrez")

## <a name="modifying-texture-coordinates"></a>Modificar coordenadas de textura

Monogame usa coordenadas de textura normalizadas, que son coordenadas entre 0 y 1, en lugar de entre 0 y el ancho o alto de la textura. El diagrama siguiente puede ayudarle a visualizar las coordenadas normalizadas:

![](part2-images/image9.png "Este diagrama puede ayudar a visualizar coordenadas normalizadas.")

Las coordenadas de textura normalizadas permiten el cambio de tamaño de las texturas sin necesidad de volver a escribir el código ni volver a crear los modelos (como los archivos. FBX). Esto es posible porque las coordenadas normalizadas representan una relación en lugar de píxeles específicos. Por ejemplo, (1,1) siempre representará la esquina inferior derecha, independientemente del tamaño de la textura.

Podemos cambiar la asignación de coordenadas de textura para usar una única variable para el número de repeticiones:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

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

    base.Initialize ();
}
```

Esto da como resultado que la textura se repita 20 veces:

![](part2-images/image10.png "Esto hace que la textura se repita 20 veces")


## <a name="rendering-vertices-with-models"></a>Representar vértices con modelos

Ahora que nuestro plano se representa correctamente, podemos volver a agregar los modelos para verlos todos juntos. En primer lugar, volveremos a agregar el código del modelo `Game1.Draw` a nuestro método (con las posiciones modificadas):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
}
```

También se creará un `Vector3` en `Game1` para representar la posición de la cámara. Vamos a agregar un campo bajo nuestra `checkerboardTexture` declaración:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10);
```

A continuación, quite la `cameraPosition` variable local del método:`DrawModel`

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            ...
```

De forma similar, `cameraPosition` Quite la variable local del método:`DrawGround`

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ...
```

Ahora, Si ejecutamos el código, podremos ver ambos modelos y el suelo al mismo tiempo:

![](part2-images/image11.png "Los modelos y el suelo se muestran al mismo tiempo")

Si se modifica la posición de la cámara (por ejemplo, al aumentar su valor X, que en este caso mueve la cámara hacia la izquierda), podemos ver que el valor afecta tanto al suelo como a los modelos:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Este código da como resultado lo siguiente:

![](part2-images/image3.png "Este código genera esta vista")

## <a name="summary"></a>Resumen

En este tutorial se ha mostrado cómo usar una matriz de vértices para realizar una representación personalizada. En este caso, hemos creado un plano inferior mediante la combinación de la representación basada en vértices con una textura `BasicEffect`y, pero el código presentado aquí sirve como base para cualquier representación 3D. También mostramos que la representación basada en vértices se puede mezclar con modelos de la misma escena.

## <a name="related-links"></a>Vínculos relacionados

- [Archivo de tablero de ajedrez (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Proyecto completado (ejemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)
