---
title: Programar UrhoSharp conF#
description: En este documento se describe cómo crear una sencilla aplicación Hola mundo UrhoSharp F# mediante en Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: d87749bd74cf2c478e96284060fed7386d10b853
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69621002"
---
# <a name="programming-urhosharp-with-f"></a>Programar UrhoSharp con F\#

UrhoSharp se puede programar con F# las mismas bibliotecas y conceptos que C# usan los programadores. El artículo [uso de UrhoSharp](~/graphics-games/urhosharp/using.md) proporciona información general sobre el motor de UrhoSharp y debe leerse antes de este artículo.

Al igual que muchas bibliotecas que se originaron en el C++ mundo, muchas funciones UrhoSharp devuelven valores booleanos o enteros que indican que se ha realizado correctamente o se ha producido un error. Debe usar `|> ignore` para omitir estos valores.

El [programa de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) es un "Hola mundo" de UrhoSharp F#de.

## <a name="creating-an-empty-project"></a>Crear un proyecto vacío

No hay ninguna F# plantilla para UrhoSharp disponible, por lo que para crear su propio proyecto de UrhoSharp puede empezar con el [ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o seguir estos pasos:

1. En Visual Studio para Mac, cree una nueva **solución**. Elija **iOS > aplicación > aplicación de vista única** y **F#** seleccione como lenguaje de implementación. 
1. Elimine el archivo **Main. Storyboard** . Abra el archivo **info. plist** y, en el panel de **información de implementación de iPhone/iPod** , elimine la `Main` cadena en la lista desplegable **interfaz principal** .
1. Elimine también el archivo **ViewController. FS** .

## <a name="building-hello-world-in-urho"></a>Compilar Hola mundo en Urho

Ahora está listo para empezar a definir las clases del juego. Como mínimo, debe definir una subclase de `Urho.Application` e invalidar su `Start` método. Para crear este archivo, haga clic con el botón F# derecho en el proyecto, elija **Agregar nuevo archivo...** y F# agregue una clase vacía al proyecto. El nuevo archivo se agregará al final de la lista de archivos del proyecto, pero debe arrastrarlo para que aparezca *antes* de que se use en **AppDelegate. FS**.

1. Agregue una referencia al paquete NuGet de Urho.
1. Desde un proyecto de Urho existente, copie los directorios (grandes) **CoreData/** y los **datos/** en el directorio **o los recursos** del proyecto. En el F# proyecto, haga clic con el botón derecho en la carpeta **recursos** y use **Agregar o Agregar carpeta existente** para agregar todos estos archivos al proyecto.

La estructura del proyecto debería tener ahora el siguiente aspecto:

![](fsharp-images/solutionpane.png "La estructura del proyecto debe ser ahora similar a")

Defina la clase recién creada como un subtipo de `Urho.Application` e invalide su `Start` método:

```fsharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

El código es muy sencillo. Usa la clase `Urho.Gui.Text` para mostrar una cadena alineada en el centro con un determinado tamaño de fuente y color. 

Sin embargo, antes de que se pueda ejecutar este código, se debe inicializar UrhoSharp. 

Abra el archivo AppDelegate. FS y modifique el `FinishedLaunching` método de la siguiente manera:

```fsharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

`ApplicationOptions.Default` Proporciona las opciones predeterminadas para una aplicación de modo horizontal. Pase estos `ApplicationOptions` al constructor predeterminado para la `Application` subclase (tenga en cuenta que al definir la `HelloWorld` clase, la línea `inherit Application(o)` llama al constructor de clase base).

El `Run` método`Application` de inicia el programa. Se define como una `int`devolución de, que se puede canalizar a. `ignore`

El programa resultante debe ser similar a esta captura de pantalla:

![Captura de pantalla del programa resultante](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>Vínculos relacionados

- [Examinar en GitHub (ejemplo)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
