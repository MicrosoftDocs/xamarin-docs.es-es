---
title: "Using CocosSharp in Xamarin.Forms " Description: "CocosSharp se puede usar para agregar una representación precisa de la forma, la imagen y el texto a una aplicación para visualización avanzada" MS. Prod: Xamarin ms. AssetID: E0F404D5-5C6B-4288-92EC-78996C674E4E ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/03/2016 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="using-cocossharp-in-xamarinforms"></a>Usar CocosSharp enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp se puede usar para agregar una representación precisa de la forma, la imagen y el texto a una aplicación para visualización avanzada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolucione 2016: cocos # inXamarin.Forms**

## <a name="overview"></a>Información general

CocosSharp es una tecnología flexible y eficaz para mostrar gráficos, leer entradas táctiles, reproducir audio y administrar contenido. En esta guía se explica cómo agregar CocosSharp a una Xamarin.Forms aplicación.

## <a name="what-is-cocossharp"></a>¿Qué es CocosSharp?

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) es un motor de juegos de código abierto que está disponible en la plataforma Xamarin.
CocosSharp es una biblioteca eficaz en tiempo de ejecución que incluye las siguientes características:

- Representación de imágenes mediante la `CCSprite` clase
- Representación de la forma mediante la `CCDrawNode` clase
- Lógica de cada fotograma mediante la `CCNode.Schedule` clase
- Administración de contenido (cargar y descargar recursos como archivos. png) mediante`CCTextureCache`
- Animaciones que utilizan la `CCAction` clase

El enfoque principal de CocosSharp es simplificar la creación de juegos 2D multiplataforma; sin embargo, también puede ser una excelente adición a las aplicaciones de Xamarin Forms. Dado que los juegos suelen requerir una representación eficaz y un control preciso de los objetos visuales, CocosSharp se puede usar para agregar una gran visualización y efectos a las aplicaciones que no son de juegos.

Xamarin.Formsse basa en sistemas de interfaz de usuario nativos y específicos de la plataforma. Por ejemplo, [ `Button` s](xref:Xamarin.Forms.Button) aparecen de forma diferente en iOS y Android, e incluso pueden variar según la versión del sistema operativo. Por el contrario, CocosSharp no usa ningún objeto visual específico de la plataforma, por lo que todos los objetos visuales aparecen idénticos en todas las plataformas. Por supuesto, la resolución y la relación de aspecto difieren entre los dispositivos y esto puede afectar a la forma en que CocosSharp representa sus objetos visuales. Estos detalles se tratarán más adelante en esta guía.

Puede encontrar información más detallada en la [sección CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

## <a name="adding-the-cocossharp-nuget-packages"></a>Adición de los paquetes NuGet de CocosSharp

Antes de usar CocosSharp, los desarrolladores deben hacer algunas adiciones a su Xamarin.Forms proyecto.
En esta guía se da por supuesto que se trata de un Xamarin.Forms proyecto con un proyecto de biblioteca de iOS, Android y .net Standard.
Todo el código se escribirá en el proyecto de biblioteca de .NET Standard; sin embargo, se deben agregar bibliotecas a los proyectos de iOS y Android.

El paquete NuGet CocosSharp contiene todos los objetos necesarios para crear objetos CocosSharp.
El paquete NuGet CocosSharp. Forms incluye la `CocosSharpView` clase, que se usa para hospedar CocosSharp en Xamarin.Forms .
Agregue los **CocosSharp. Forms** NuGet y **CocosSharp** también se agregarán automáticamente.
Para ello, haga clic con el botón derecho en la carpeta **paquetes** en el proyecto de biblioteca de .net Standard y seleccione **agregar paquetes.**... Escriba el término de búsqueda **CocosSharp. Forms**, seleccione **CocosSharp para Xamarin.Forms **y, a continuación, haga clic en **Agregar paquete**.

![](cocossharp-images/image1.png "Add Packages Dialog")

Los paquetes NuGet **CocosSharp** y **CocosSharp. Forms** se agregarán al proyecto:

![](cocossharp-images/image2.png "Packages Folder")

Repita los pasos anteriores para los proyectos específicos de la plataforma (como iOS y Android).

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Tutorial: agregar CocosSharp a una Xamarin.Forms aplicación

Siga estos pasos para agregar una vista CocosSharp sencilla a una Xamarin.Forms aplicación:

1. [Crear una página de Xamarin Forms](#1-creating-a-xamarin-forms-page)
1. [Adición de un CocosSharpView](#2-adding-a-cocossharpview)
1. [Crear GameScene](#3-creating-the-gamescene)
1. [Agregar un círculo](#4-adding-a-circle)
1. [Interacción con CocosSharp](#5-interacting-with-cocossharp)

Una vez que haya agregado correctamente una vista de CocosSharp a una Xamarin.Forms aplicación, visite la [documentación de CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) para obtener más información sobre la creación de contenido con CocosSharp.

### <a name="1-creating-a-xamarin-forms-page"></a>1. crear una página de Xamarin Forms

CocosSharp se puede hospedar en cualquier Xamarin.Forms contenedor. En este ejemplo de esta página se usa una página denominada `HomePage` . `HomePage`se divide en la mitad por un `Grid` para mostrar cómo Xamarin.Forms y CocosSharp se pueden representar simultáneamente en la misma página.

En primer lugar, configure la página para que contenga una `Grid` y dos `Button` instancias:

```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

En iOS, `HomePage` aparece como se muestra en la siguiente imagen:

![](cocossharp-images/image3.png "HomePage Screenshot")

### <a name="2-adding-a-cocossharpview"></a>2. agregar CocosSharpView

La `CocosSharpView` clase se usa para incrustar CocosSharp en una Xamarin.Forms aplicación. Puesto que `CocosSharpView` hereda de [ Xamarin.Forms . ](xref:Xamarin.Forms.View)La clase de vista, proporciona una interfaz conocida para el diseño y se puede usar dentro de contenedores de diseño como [ Xamarin.Forms . Cuadrícula](xref:Xamarin.Forms.Grid). Agregue un nuevo `CocosSharpView` al proyecto completando el `CreateTopHalf` método:

```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

La inicialización de CocosSharp no es inmediata, por lo que debe registrar un evento para cuando `CocosSharpView` haya finalizado su creación. Haga esto en el `HandleViewCreated` método:

```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

El `HandleViewCreated` método tiene dos detalles importantes que se van a examinar. La primera es la `GameScene` clase, que se creará en la sección siguiente. Es importante tener en cuenta que la aplicación no se compilará hasta que `GameScene` se cree y `gameScene` se resuelva la referencia de la instancia.

El segundo detalle importante es la `DesignResolution` propiedad, que define el área visible del juego para los objetos CocosSharp. La `DesignResolution` propiedad se examinará después de crear `GameScene` .

### <a name="3-creating-the-gamescene"></a>3. crear GameScene

La `GameScene` clase hereda de CocosSharp `CCScene` . `GameScene`es el primer punto en el que trataremos exclusivamente con CocosSharp. El código contenido en funcionará `GameScene` en cualquier aplicación de CocosSharp, tanto si está hospedado dentro de un Xamarin.Forms proyecto como si no.

La `CCScene` clase es la raíz visual de toda la representación de CocosSharp. Cualquier objeto CocosSharp visible debe estar incluido en `CCScene` . Más concretamente, los objetos visuales se deben agregar a `CCLayer` las instancias de y esas `CCLayer` instancias se deben agregar a `CCScene` .

El siguiente gráfico puede ayudarle a visualizar una jerarquía de CocosSharp típica:

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

Solo una `CCScene` puede estar activa al mismo tiempo. La mayoría de los juegos usan varias `CCLayer` instancias para ordenar el contenido, pero nuestra aplicación solo usa una. Del mismo modo, la mayoría de los juegos usan varios objetos visuales, pero solo tendremos uno en nuestra aplicación. Puede encontrar una explicación más detallada sobre la jerarquía visual CocosSharp en el [tutorial de BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inicialmente, la `GameScene` clase estará casi vacía, simplemente la crearemos para satisfacer la referencia en `HomePage` . Agregue una nueva clase al proyecto de biblioteca de .NET Standard denominado `GameScene` . Debe heredar de la `CCScene` clase de la siguiente manera:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Ahora que `GameScene` está definido, podemos volver a `HomePage` y agregar un campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Ahora podemos compilar el proyecto y ejecutarlo para ver la ejecución de CocosSharp. No hemos agregado nada a nuestro `GameScene,` , por lo que la mitad superior de la página es negra, el color predeterminado de una escena de CocosSharp:

![](cocossharp-images/image5.png "Blank GameScene")

### <a name="4-adding-a-circle"></a>4. agregar un círculo

La aplicación actualmente tiene una instancia en ejecución del motor CocosSharp, que muestra un vacío `CCScene` . A continuación, agregaremos un objeto visual: un círculo. La `CCDrawNode` clase se puede usar para dibujar una variedad de formas geométricas, tal y como se describe en la [Guía de dibujo de Geometry with CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Agregue un círculo a nuestra `GameScene` clase y cree una instancia de él en el constructor tal y como se muestra en el código siguiente:

```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

Al ejecutar la aplicación ahora se muestra un círculo en el lado izquierdo del área de presentación de CocosSharp:

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>Descripción de DesignResolution

Ahora que se muestra un objeto visual CocosSharp, podemos investigar la `DesignResolution` propiedad.

`DesignResolution`Representa el ancho y el alto del área CocosSharp para colocar y ajustar el tamaño de los objetos. La resolución real del área se mide en *píxeles* mientras `DesignResolution` se mide en *unidades*universales. En el diagrama siguiente se muestra la resolución de varias partes de la vista, tal como se muestra en un iPhone 5 con una resolución de pantalla de 640x1136 píxeles:

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

El diagrama anterior muestra las dimensiones de píxeles en el exterior de la pantalla en texto en negro. Las unidades se muestran en el interior del diagrama en texto en blanco. Estos son algunos detalles importantes mostrados anteriormente:

- El origen de la pantalla de CocosSharp está en la parte inferior izquierda. Al moverse a la derecha, se aumenta el valor X y se aumenta el valor Y. Observe que el valor Y se invierte en comparación con otros motores de diseño 2D, donde (0,0) es la parte superior izquierda del lienzo.
- El comportamiento predeterminado de CocosSharp es mantener la relación de aspecto de su vista. Como la primera fila de la cuadrícula es más ancha que la de alta, CocosSharp no rellena todo el ancho de la celda, tal como se muestra en el rectángulo blanco con puntos. Este comportamiento se puede cambiar, tal como se describe en la [Guía Handling Multiple Solutions in CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- En este ejemplo, CocosSharp mantendrá un área de presentación de 100 unidades de ancho y alto, independientemente del tamaño o la relación de aspecto de su dispositivo. Esto significa que el código puede suponer que X = 100 representa el límite de la derecha de la pantalla CocosSharp, manteniendo el diseño coherente en todos los dispositivos.

#### <a name="ccdrawnode-details"></a>Detalles de CCDrawNode

Nuestra aplicación simple usa la `CCDrawNode` clase para dibujar un círculo. Esta clase puede ser muy útil para las aplicaciones empresariales, ya que proporciona representación de geometría basada en vectores: falta una característica de Xamarin.Forms . Además de los círculos, la `CCDrawNode` clase se puede usar para dibujar rectángulos, curvas spline, líneas y polígonos personalizados. `CCDrawNode`también es fácil de usar, ya que no requiere el uso de archivos de imagen (como. png). Puede encontrar una explicación más detallada de CCDrawNode en la [Guía de dibujo de geometría con CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

### <a name="5-interacting-with-cocossharp"></a>5. interactuar con CocosSharp

Los elementos visuales de CocosSharp (como `CCDrawNode` ) se heredan de la `CCNode` clase. `CCNode`proporciona dos propiedades que se pueden usar para colocar un objeto con respecto a su elemento primario: `PositionX` y `PositionY` . En este momento, nuestro código usa estas dos propiedades para colocar el centro del círculo, tal como se muestra en este fragmento de código:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

Es importante tener en cuenta que los objetos CocosSharp se colocan por valores de posición explícitos, en lugar de la mayoría de Xamarin.Forms las vistas, que se colocan automáticamente según el comportamiento de sus controles de diseño primarios.

Agregaremos código para permitir que el usuario haga clic en uno de los dos botones para desplace el círculo a la izquierda o a la derecha en 10 unidades (no en píxeles, ya que el círculo se dibuja en el espacio de la unidad mundial de CocosSharp). En primer lugar, vamos a crear dos métodos públicos en la `GameScene` clase:

```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

A continuación, agregaremos controladores a los dos botones de `HomePage` para responder a los clics. Cuando termine, nuestro `CreateBottomHalf` método contiene el código siguiente:

```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

El círculo CocosSharp ahora se mueve en respuesta a los clics. También podemos ver claramente los límites del lienzo CocosSharp moviendo el círculo lo suficientemente lejos hacia la izquierda o la derecha:

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>Resumen

En esta guía se muestra cómo agregar CocosSharp a un Xamarin.Forms proyecto existente, cómo crear una interacción entre Xamarin.Forms y CocosSharp, y se explican varias consideraciones al crear diseños en CocosSharp.

El motor de juegos de CocosSharp ofrece una gran cantidad de funcionalidad y profundidad, por lo que esta guía solo borra la superficie de lo que puede hacer CocosSharp. Los desarrolladores interesados en leer más sobre CocosSharp pueden encontrar muchos artículos en el [archivo CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Vínculos relacionados

- [CocosSharpForms (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
