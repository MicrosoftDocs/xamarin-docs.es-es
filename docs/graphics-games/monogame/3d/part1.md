---
title: Usar la clase Model
description: La clase de modelo simplifica considerablemente la representación de objetos 3D complejos en comparación con el método tradicional de representación de gráficos 3D. Los objetos de modelo se crean a partir de archivos de contenido, lo que permite una fácil integración del contenido sin código personalizado.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 35df6e4ca799a875bcd7db50adbc7a300460885c
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680962"
---
# <a name="using-the-model-class"></a>Usar la clase Model

_La clase de modelo simplifica considerablemente la representación de objetos 3D complejos en comparación con el método tradicional de representación de gráficos 3D. Los objetos de modelo se crean a partir de archivos de contenido, lo que permite una fácil integración del contenido sin código personalizado._

La API de monogame incluye `Model` una clase que se puede usar para almacenar datos cargados de un archivo de contenido y para realizar la representación. Los archivos de modelo pueden ser muy sencillos (por ejemplo, un triángulo de color sólido) o pueden incluir información para una representación compleja, como texturas e iluminación.

En este tutorial se usa [un modelo 3D de un robot](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) y se trata lo siguiente:

- Iniciar un nuevo proyecto de juego
- Crear XNBs para el modelo y su textura
- Incluir XNBs en el proyecto de juego
- Dibujo de un modelo 3D
- Dibujar varios modelos

Cuando termine, el proyecto aparecerá de la siguiente manera:

![Ejemplo finalizado que muestra seis robots](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Crear un proyecto de juego vacío

Tendremos que configurar un proyecto de juego denominado primero MonoGame3D. Para obtener información sobre cómo crear un nuevo proyecto monogame, consulte [este tutorial sobre cómo crear un proyecto multiplataforma monogame](~/graphics-games/monogame/introduction/part1.md).

Antes de continuar, debemos comprobar que el proyecto se abre y se implementa correctamente. Una vez implementada, deberíamos ver una pantalla azul vacía:

![Pantalla de juego azul en blanco](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>Incluir XNBs en el proyecto de juego

El formato de archivo. XNB es una extensión estándar para el contenido compilado (contenido creado por la [herramienta](http://www.monogame.net/documentation/?page=Pipeline)de canalización monogame). Todo el contenido compilado tiene un archivo de código fuente (que es un archivo. FBX en el caso de nuestro modelo) y un archivo de destino (un archivo. XNB). El formato .fbx es un formato de modelo 3D común que pueden crear aplicaciones como [Maya](http://www.autodesk.com/products/maya/overview) y [Blender](http://www.blender.org/). 

La `Model` clase se puede construir cargando un archivo. XNB de un disco que contenga datos de geometría 3D.   Este archivo. XNB se crea a través de un proyecto de contenido. Las plantillas monogame incluyen automáticamente un proyecto de contenido (con la extensión. MGCP) en la carpeta de contenido. Para obtener información detallada sobre la herramienta de canalización de monogame, consulte la guía de canalizaciones de [contenido](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

En esta guía se omitirá el uso de la herramienta de canalización monogame y se usará. XNB los archivos que se incluyen aquí. Tenga en cuenta que. Los archivos XNB varían según la plataforma, por lo que debe asegurarse de usar el conjunto correcto de archivos XNB para cualquier plataforma con la que esté trabajando.

Descomprimiremos el [archivo. zip de contenido](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) para que podamos usar los archivos. XNB contenidos en el juego. Si trabaja en un proyecto de Android, haga clic con el botón derecho en la carpeta **assets** del proyecto **WalkingGame. Android** . Si trabaja en un proyecto de iOS, haga clic con el botón derecho en el proyecto **WalkingGame. iOS** . Seleccione **agregar > agregar archivos...** y seleccione ambos archivos. XNB en la carpeta de la plataforma en la que está trabajando.

Los dos archivos deben formar parte de nuestro proyecto ahora:

![Carpeta de contenido del explorador de soluciones con archivos XNB](part1-images/xnbsinxs.png)

Es posible que Visual Studio para Mac no establezca automáticamente la acción de compilación para XNBs recién agregados. Para iOS, haga clic con el botón derecho en cada uno de los archivos y seleccione **Build Action-> BundleResource**. Para Android, haga clic con el botón derecho en cada uno de los archivos y seleccione **Build Action-> AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Representar un modelo 3D

El último paso necesario para ver el modelo en pantalla consiste en agregar el código de carga y de dibujo. En concreto, haremos lo siguiente:

- Definir una `Model` instancia en nuestra `Game1` clase
- Cargar la `Model` instancia en`Game1.LoadContent`
- Dibujar la `Model` instancia en`Game1.Draw`

Reemplace el `Game1.cs` archivo de código (que se encuentra en la PCL de **WalkingGame** ) por lo siguiente:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Si ejecutamos este código, veremos el modelo en pantalla:

![Modelo mostrado en pantalla](part1-images/image8.png "Si se ejecuta este código, el modelo se mostrará en pantalla") .

### <a name="model-class"></a>Clase de modelo

La `Model` clase es la clase principal para realizar la representación 3D a partir de archivos de contenido (como archivos. FBX). Contiene toda la información necesaria para la representación, incluidas la geometría 3D, las referencias de textura y `BasicEffect` las instancias que controlan los valores de posición, iluminación y cámara.

La `Model` propia clase no tiene directamente variables de posicionamiento, ya que una única instancia de modelo se puede representar en varias ubicaciones, como se muestra más adelante en esta guía.

Cada `Model` se compone de una o varias `ModelMesh` instancias de, que se exponen a través de la `Meshes` propiedad. Aunque podamos considerar `Model` como un solo objeto de juego (por ejemplo, un robot o un coche), cada `ModelMesh` uno de ellos se puede `BasicEffect` dibujar con valores diferentes. Por ejemplo, las partes individuales de la malla pueden representar las piernas de un robot o las ruedas de un coche, y es `BasicEffect` posible que se asignen los valores para que las ruedas giren o se muevan las piernas. 

### <a name="basiceffect-class"></a>Clase BasicEffect

La `BasicEffect` clase proporciona propiedades para controlar las opciones de representación. La primera modificación que hacemos en `BasicEffect` es `EnableDefaultLighting` llamar al método. Como implica el nombre, esto habilita la iluminación predeterminada, que es muy útil para comprobar que un `Model` aparece en el juego como se espera. Si marcamos como comentario `EnableDefaultLighting` la llamada, veremos que el modelo se representará con solo su textura, pero sin sombreado ni resplandor especular:

```csharp
//effect.EnableDefaultLighting ();
```

![Modelo representado solo con su textura, pero sin sombreado ni resplandor especular](part1-images/image9.png "Modelo representado solo con su textura, pero sin sombreado ni resplandor especular")

La `World` propiedad se puede utilizar para ajustar la posición, el giro y la escala del modelo. El código anterior usa el `Matrix.Identity` valor, lo que significa `Model` que se representará en juego exactamente como se especifica en el archivo. FBX. Trataremos las matrices y las coordenadas 3D con más detalle en la [parte 3](~/graphics-games/monogame/3d/part3.md), pero, como ejemplo, podemos cambiar la posición de `Model` cambiando la `World` propiedad de la manera siguiente:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Este código hace que el objeto se mueva hacia arriba en tres unidades universales:

![Este código hace que el objeto se mueva hacia arriba en tres unidades universales](part1-images/image10.png "Este código hace que el objeto se mueva hacia arriba en tres unidades universales")

Las dos propiedades finales asignadas en `BasicEffect` son `View` y `Projection`. Vamos a cubrir las cámaras 3D en la [parte 3](~/graphics-games/monogame/3d/part3.md), pero, por ejemplo, podemos modificar la posición de la cámara cambiando la variable local `cameraPosition` :

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Podemos ver que la cámara ha retrovuelto, lo que da lugar `Model` a un tamaño menor debido a la perspectiva:

![La cámara se ha vuelto más allá, lo que da lugar a que el modelo aparezca más pequeño debido a la perspectiva](part1-images/image11.png "La cámara se ha vuelto más allá, lo que da lugar a que el modelo aparezca más pequeño debido a la perspectiva")

## <a name="rendering-multiple-models"></a>Representar varios modelos

Como se mencionó anteriormente, un `Model` solo se puede dibujar varias veces. Para facilitar esta tarea, moveremos el código `Model` de dibujo a su propio método que toma la posición `Model` deseada como parámetro. Una vez finalizado `Draw` , `DrawModel` nuestros métodos y tendrán el siguiente aspecto:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;
            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;
            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

Esto hace que el modelo de robot se dibuje seis veces:

![Esto hace que el modelo de robot se dibuje seis veces](part1-images/image1.png "Esto hace que el modelo de robot se dibuje seis veces")

## <a name="summary"></a>Resumen

En este tutorial se ha introducido `Model` la clase de monogame. Trata la conversión de un archivo. FBX en un. XNB, que se puede cargar a la vez en `Model` una clase. También muestra cómo las modificaciones de una `BasicEffect` instancia pueden afectar `Model` al dibujo.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia del modelo monogame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Archivo. zip de contenido](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Proyecto completado (ejemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)
