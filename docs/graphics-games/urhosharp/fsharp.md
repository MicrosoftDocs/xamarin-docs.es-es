---
title: Programación de UrhoSharp con F#
description: 'En este documento se describe cómo crear una sencilla aplicación Hello World UrhoSharp con F # en Visual Studio para Mac.'
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: af9619ace957a47282cbf9fdefea4e81e7eace13
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86940015"
---
# <a name="programming-urhosharp-with-f"></a>Programar UrhoSharp con F\#

UrhoSharp se puede programar con F # usando las mismas bibliotecas y conceptos que usan los programadores de C#. El artículo [uso de UrhoSharp](~/graphics-games/urhosharp/using.md) proporciona información general sobre el motor de UrhoSharp y debe leerse antes de este artículo.

Al igual que muchas bibliotecas que se originaron en el mundo de C++, muchas funciones de UrhoSharp devuelven valores booleanos o enteros que indican que se ha realizado correctamente o se ha producido un error. Debe usar `|> ignore` para omitir estos valores.

El [programa de ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) es un "Hola mundo" para UrhoSharp de F #.

## <a name="creating-an-empty-project"></a>Crear un proyecto vacío

No hay ninguna plantilla de F # para UrhoSharp disponible, por lo que para crear su propio proyecto de UrhoSharp puede empezar con el [ejemplo](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o seguir estos pasos:

1. En Visual Studio para Mac, cree una nueva **solución**. Elija **iOS > app > aplicación de vista única** y seleccione **F #** como lenguaje de implementación. 
1. Elimine el archivo **Main. Storyboard** . Abra el archivo **info. plist** y, en el panel de **información de implementación de iPhone/iPod** , elimine la `Main` cadena en la lista desplegable **interfaz principal** .
1. Elimine también el archivo **ViewController. FS** .

## <a name="building-hello-world-in-urho"></a>Compilar Hola mundo en Urho

Ahora está listo para empezar a definir las clases del juego. Como mínimo, debe definir una subclase de `Urho.Application` e invalidar su `Start` método. Para crear este archivo, haga clic con el botón derecho en el proyecto de F #, elija **Agregar nuevo archivo...** y agregue una clase vacía de F # al proyecto. El nuevo archivo se agregará al final de la lista de archivos del proyecto, pero debe arrastrarlo para que aparezca *antes* de que se use en **AppDelegate. FS**.

1. Agregue una referencia al paquete NuGet de Urho.
1. Desde un proyecto de Urho existente, copie los directorios (grandes) **CoreData/** y los **datos/** en el directorio **o los recursos** del proyecto. En el proyecto de F #, haga clic con el botón derecho en la carpeta **recursos** y use **Agregar o Agregar carpeta existente** para agregar todos estos archivos al proyecto.

La estructura del proyecto debería tener ahora el siguiente aspecto:

![La estructura del proyecto debe ser ahora similar a](fsharp-images/solutionpane.png)

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

El código es muy sencillo. Usa la `Urho.Gui.Text` clase para mostrar una cadena alineada en el centro con un determinado tamaño de fuente y color. 

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

`ApplicationOptions.Default`Proporciona las opciones predeterminadas para una aplicación de modo horizontal. Pase estos `ApplicationOptions` al constructor predeterminado para la `Application` subclase (tenga en cuenta que al definir la `HelloWorld` clase, la línea `inherit Application(o)` llama al constructor de clase base).

El `Run` método de `Application` inicia el programa. Se define como una devolución de `int` , que se puede canalizar a `ignore` .

El programa resultante debe ser similar a esta captura de pantalla:

![Captura de pantalla del programa resultante](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>Vínculos relacionados

- [Examinar en GitHub (ejemplo)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
