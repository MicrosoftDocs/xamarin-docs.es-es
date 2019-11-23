---
title: Uso de CocosSharp en Xamarin.Forms
description: CocosSharp puede utilizarse para agregar de forma precisa, la imagen y la representación de texto a una aplicación para visualización avanzada
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 7b465391958a6e862bfed9fde8d9da1fdd52bee5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759757"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Uso de CocosSharp en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](h https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp puede utilizarse para agregar de forma precisa, la imagen y la representación de texto a una aplicación para visualización avanzada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016: Cocos # en Xamarin.Forms**

## <a name="overview"></a>Información general

CocosSharp es una tecnología flexible y eficaz para la presentación de gráficos, leer la entrada táctil, reproducir contenido de audio y la administración. Esta guía explica cómo agregar CocosSharp a una aplicación de Xamarin.Forms. Se trata lo siguiente:

- [¿Qué es CocosSharp?](#what)
- [Agregar los paquetes de CocosSharp Nuget](#nuget)
- [Tutorial: Agregar CocosSharp a una aplicación de Xamarin.Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>¿Qué es CocosSharp?

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) es un motor de juegos de código abierto que está disponible en la plataforma Xamarin.
CocosSharp es una biblioteca en tiempo de ejecución eficaz que incluye las siguientes características:

- Representación de imágenes mediante la clase `CCSprite`
- Representación de la forma mediante la clase `CCDrawNode`
- Lógica de cada fotograma mediante la clase `CCNode.Schedule`
- Administración de contenido (carga y descarga de recursos como archivos. png) mediante el `CCTextureCache`
- Animaciones con la clase `CCAction`

Objetivo principal de CocosSharp es simplificar la creación de juegos en 2D multiplataforma; Sin embargo, también puede ser una gran adición a aplicaciones de Xamarin Forms. Puesto que juegos normalmente requieren la eficacia de representación y un control preciso sobre los objetos visuales, CocosSharp puede usarse para agregar una visualización eficaz y efectos a las aplicaciones que no sean juego.

Xamarin.Forms se basa en los sistemas de la interfaz de usuario nativos, específica de la plataforma. Por ejemplo, [ `Button`s](xref:Xamarin.Forms.Button) aparecen de manera diferente en iOS y Android y que incluso pueden diferir por versión del sistema operativo. Por el contrario, CocosSharp no usa los objetos visuales específicos de la plataforma, por lo que todos los objetos visuales aparecen idénticos en todas las plataformas. Por supuesto, resolución y la relación de aspecto difieren entre dispositivos, y esto puede afectar a cómo se CocosSharp representa los objetos visuales. Estos detalles se tratarán más adelante en esta guía.

Puede encontrar información más detallada en el [CocosSharp sección](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Agregar los paquetes de CocosSharp Nuget

Antes de usar CocosSharp, los desarrolladores necesitan realizar algunas adiciones a su proyecto de Xamarin.Forms.
Esta guía se da por supuesto un proyecto de Xamarin.Forms con un iOS, Android y .NET Standard proyecto de biblioteca.
Todo el código se escribirá en el proyecto de biblioteca .NET Standard; Sin embargo, las bibliotecas deben agregarse a los proyectos de Android y iOS.

El paquete de CocosSharp Nuget contiene todos los objetos necesarios para crear objetos de CocosSharp.
El paquete de nuget CocosSharp.Forms incluye la `CocosSharpView` (clase), que se usa para el host de CocosSharp en Xamarin.Forms.
Agregar el **CocosSharp.Forms** NuGet y **CocosSharp** se agregarán automáticamente también.
Para ello, haga clic con el botón derecho en la carpeta **paquetes** en el proyecto de biblioteca de .net Standard y seleccione **agregar paquetes.** ... Escriba el término de búsqueda **CocosSharp. Forms**, seleccione **CocosSharp para Xamarin. Forms**y haga clic en **Agregar paquete**.

![](cocossharp-images/image1.png "Agregar cuadro de diálogo paquetes")

Ambos **CocosSharp** y **CocosSharp.Forms** paquetes de NuGet se agregarán al proyecto:

![](cocossharp-images/image2.png "Carpeta de paquetes")

Repita los pasos anteriores para los proyectos específicos de la plataforma (por ejemplo, iOS y Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Tutorial: Agregar CocosSharp a una aplicación de Xamarin.Forms

Siga estos pasos para agregar una vista sencilla de CocosSharp a una aplicación de Xamarin.Forms:

1. [Crear un Xamarin Forms página](#1)
1. [Agregar un CocosSharpView](#2)
1. [Crear el GameScene](#3)
1. [Adición de un círculo](#4)
1. [Interactuar con CocosSharp](#5)

Una vez que se ha agregado correctamente una vista de CocosSharp en una aplicación de Xamarin.Forms, visite la [CocosSharp documentación](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) para obtener más información sobre la creación de contenido con CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. crear una página de Xamarin Forms

CocosSharp puede hospedarse en cualquier contenedor de Xamarin.Forms. Este ejemplo de esta página utiliza una página denominada `HomePage`. `HomePage` se divide en dos por un `Grid` para mostrar cómo Xamarin.Forms y CocosSharp se pueden representar simultáneamente en la misma página.

En primer lugar, configure la página de manera que contenga un `Grid` y dos `Button` instancias:

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

En iOS, el `HomePage` aparece como se muestra en la siguiente imagen:

![](cocossharp-images/image3.png "Captura de pantalla de página principal")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. agregar CocosSharpView

La `CocosSharpView` clase se utiliza para insertar CocosSharp en una aplicación de Xamarin.Forms. Puesto que `CocosSharpView` hereda el [Xamarin.Forms.View](xref:Xamarin.Forms.View) (clase), proporciona una interfaz conocida para el diseño, y puede utilizarse dentro de contenedores de diseño como [Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid). Agregue un nuevo `CocosSharpView` al proyecto siguiendo el `CreateTopHalf` método:

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

Inicialización de CocosSharp no es inmediata, por lo que se registrará un evento para cuando el `CocosSharpView` ha finalizado su creación. Hacer esto en el `HandleViewCreated` método:

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

El `HandleViewCreated` método tiene dos detalles importantes que buscaremos en. La primera es la `GameScene` (clase), que se crearán en la sección siguiente. Es importante tener en cuenta que la aplicación no se compilará hasta que el `GameScene` se crea y `gameScene` se resuelve la referencia a la instancia.

El segundo detalle importante es la `DesignResolution` propiedad, que define el área visible del juego CocosSharp objetos. El `DesignResolution` se analizan propiedad después de crear `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. crear GameScene

El `GameScene` clase hereda de CocosSharp `CCScene`. `GameScene` es el primer punto donde se tratan exclusivamente con CocosSharp. Código contenido en `GameScene` funcionará en cualquier aplicación CocosSharp, si está alojado dentro de un proyecto de Xamarin.Forms o no.

La `CCScene` clase es la raíz visual de la representación de CocosSharp todas. Debe estar dentro de cualquier objeto visible de CocosSharp un `CCScene`. Más concretamente, se deben agregar objetos visuales a `CCLayer` instancias y los `CCLayer` instancias deben agregarse a un `CCScene`.

El gráfico siguiente puede ayudar a visualizar una jerarquía típica de CocosSharp:

![](cocossharp-images/image4.png "Jerarquía típica de CocosSharp")

Solo un `CCScene` pueden estar activas al mismo tiempo. La mayoría de juegos usa varios `CCLayer` instancias para el contenido de ordenación, pero nuestra aplicación utiliza sólo uno. De forma similar, la mayoría de juegos usa varios objetos visuales, pero solo tendremos una en nuestra aplicación. Más detallada de debate sobre el CocosSharp jerarquía visual puede encontrarse en el [BouncingGame tutorial](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inicialmente el `GameScene` clase será casi vacía: simplemente vamos a crear para satisfacer la referencia en `HomePage`. Agregue una nueva clase al proyecto Biblioteca .NET Standard denominado `GameScene`. Debe heredar la `CCScene` clase como sigue:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Ahora que `GameScene` está definido, podemos volver a `HomePage` y agregue un campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Ahora podemos compilar el proyecto y ejecútelo para ver la ejecución de CocosSharp. Algo que no hayamos agregado nuestro `GameScene,` por lo que la mitad superior de la página es el negro: el color predeterminado de una escena de CocosSharp:

![](cocossharp-images/image5.png "GameScene en blanco")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. agregar un círculo

Actualmente, la aplicación tiene una instancia en ejecución del motor de CocosSharp, mostrar un valor vacío `CCScene`. A continuación, vamos a agregar un objeto visual: un círculo. El `CCDrawNode` clase puede utilizarse para dibujar una variedad de formas geométricas, como se describe en el [dibujar geometría con CCDrawNode guía](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Adición de un círculo a nuestro `GameScene` clase y crear instancias en el constructor como se muestra en el código siguiente:

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

Ejecutar la aplicación ahora muestra un círculo en el lado izquierdo del área de visualización de CocosSharp:

![](cocossharp-images/image6.png "Círculo en GameScene")

#### <a name="understanding-designresolution"></a>Descripción DesignResolution

Ahora que se muestra un objeto visual de CocosSharp, podemos investigar la `DesignResolution` propiedad.

El `DesignResolution` representa el ancho y alto del área de CocosSharp para colocar y ajustar el tamaño de los objetos. La resolución real de la zona se mide en *píxeles* mientras el `DesignResolution` se mide en el mundo *unidades*. El siguiente diagrama muestra la resolución de varias partes de la vista, tal como se muestra en un iPhone 5 con una resolución de pantalla de 640 x 1136 píxeles:

![](cocossharp-images/image7.png "iPhone 5s resolución de diseño")

El diagrama anterior muestra las dimensiones de píxeles en la parte exterior de la pantalla en negro. Las unidades se muestran en el interior del diagrama en el texto en blanco. Estos son algunos detalles importantes que se muestra anteriormente:

- El origen de la presentación de CocosSharp está en la parte inferior izquierda. Desplácese hacia la derecha aumenta el valor de X, y mover hacia arriba el valor Y. Tenga en cuenta que el valor Y se invierte en comparación con otros motores de diseño 2D, donde (0,0) es la parte superior izquierda del lienzo.
- El comportamiento predeterminado de CocosSharp es mantener la relación de aspecto de su vista. Puesto que la primera fila de la cuadrícula es más ancha alta, CocosSharp no rellena todo el ancho de la celda, como se muestra en el rectángulo blanco punteado. Se puede cambiar este comportamiento, como se describe en el [guía controlar varias resoluciones en CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- En este ejemplo, CocosSharp mantendrá un área de visualización de 100 unidades de anchos y altos, independientemente del tamaño o la relación de aspecto de su dispositivo. Esto significa que el código puede asumir que X = 100 representa dependiente de la derecha de la CocosSharp Mostrar Mantener diseño coherente en todos los dispositivos.

#### <a name="ccdrawnode-details"></a>Detalles de CCDrawNode

La simple aplicación emplea la `CCDrawNode` clases para dibujar un círculo. Esta clase puede ser muy útil para aplicaciones empresariales, ya que proporciona la representación basada en vectores geometría: una característica que falta en Xamarin.Forms. Además de círculos; el `CCDrawNode` clase puede utilizarse para dibujar rectángulos, curvas spline, líneas y polígonos personalizados. `CCDrawNode` También es fácil de usar, ya que no requiere el uso de archivos de imagen (.png). Puede encontrar una explicación más detallada de CCDrawNode en el [dibujar geometría con CCDrawNode guía](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. interactuar con CocosSharp

Elementos visuales de CocosSharp (como `CCDrawNode`) heredan la `CCNode` clase. `CCNode` proporciona dos propiedades que se pueden usar para colocar un objeto con respecto a su elemento primario: `PositionX` y `PositionY`. Actualmente, nuestro código utiliza estas dos propiedades para colocar el centro del círculo, como se muestra en este fragmento de código:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

Es importante tener en cuenta que se colocan los objetos de CocosSharp por valores de posición explícitos, en lugar de la mayoría vistas de Xamarin.Forms, que se colocan automáticamente según el comportamiento de sus controles de diseño del elemento primario.

Vamos a agregar código para permitir que el usuario haga clic en uno de los dos botones para mover el círculo a la izquierda o a la derecha 10 unidades (no píxeles, puesto que el círculo se dibuja en el espacio de unidad de CocosSharp global). Primero vamos a crear dos métodos públicos en el `GameScene` clase:

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

A continuación, vamos a agregar controladores a los dos botones de `HomePage` para responder a clics. Cuando termine, nuestro `CreateBottomHalf` método contiene el código siguiente:

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

Ahora mueve el círculo de CocosSharp en respuesta a los clics. Podemos ver claramente también los límites del lienzo CocosSharp moviendo el círculo lo suficiente a la izquierda o derecha:

![](cocossharp-images/image8.png "GameScene con el movimiento de círculo")

## <a name="summary"></a>Resumen

Esta guía muestra cómo agregar CocosSharp a un Xamarin.Forms existente del proyecto, cómo crear una interacción entre Xamarin.Forms y CocosSharp y se describen diversas consideraciones al crear diseños de CocosSharp.

El motor de juego de CocosSharp ofrece una gran cantidad de funcionalidad y la profundidad, por lo que esta guía solo la punta del iceberg de lo que puede hacer CocosSharp. Los desarrolladores interesados en leer más sobre CocosSharp pueden encontrar muchos artículos en el [archivo CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Vínculos relacionados

- [CocosSharpForms (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
