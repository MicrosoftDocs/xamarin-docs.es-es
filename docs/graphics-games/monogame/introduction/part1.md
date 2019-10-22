---
title: 'Parte 1: creación de un juego multiplataforma'
description: En este tutorial se muestra cómo crear un nuevo proyecto para iOS y Android con monogame. El resultado es una solución Visual Studio para Mac con un proyecto de código compartido multiplataforma, así como un proyecto para cada plataforma. Este proyecto mostrará una pantalla azul vacía cuando se ejecute.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d72c428bb4b8c88365180c5c3c50b107eed2b21d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68978446"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1: creación de un juego multiplataforma

_En este tutorial se muestra cómo crear un nuevo proyecto para iOS y Android con monogame. El resultado es una solución Visual Studio para Mac con un proyecto de código compartido multiplataforma, así como un proyecto para cada plataforma. Este proyecto mostrará una pantalla azul vacía cuando se ejecute._

Monogame permite desarrollar juegos multiplataforma con gran parte de la reutilización de código. Este tutorial se centrará en la configuración de una solución que contenga proyectos para iOS y Android, así como un proyecto de código compartido para código multiplataforma.

Cuando haya finalizado, el proyecto tiene la estructura adecuada para realizar la lógica de actualización de juegos y la lógica de dibujo de juegos en 30 fotogramas por segundo. Se puede usar como proyecto base para cualquier proyecto monogame. El proyecto tendrá este aspecto cuando se ejecute:

![Pantalla azul en blanco](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio"></a>Adición de monogame a Visual Studio

> [!IMPORTANT]
> Monogame no se instala de forma predeterminada en Visual Studio 2019 o Visual Studio para Mac.
>
> Debe descargar e instalar manualmente la versión más reciente de http://www.monogame.net/downloads/ después ejecutar el instalador. Es posible que tenga que reiniciar Visual Studio para que aparezcan las plantillas.
>
> La sección **desarrollo de juegos** debería aparecer en el **Administrador de complementos**.

Para habilitar el complemento de monogame para Visual Studio para Mac, seleccione **Visual Studio para Mac**  > **Administrador de complementos..** .. Para Visual Studio 2019 en Windows, seleccione **herramientas**  > **Administrador de complementos..** .. Seleccione la pestaña **Galería** , expanda la categoría **desarrollo de juegos** y seleccione **complemento monogame**y, a continuación, haga clic en **instalar...** :

![Galería de Visual Studio para Mac Extensions seleccionar monogame](part1-images/image2.png)

Una vez instalado, las plantillas de monogame aparecerán en Visual Studio para Mac, como veremos en la sección siguiente.

## <a name="creating-a-new-solution"></a>Crear una nueva solución

En Visual Studio para Mac seleccione **archivo > nueva solución**. En el cuadro de diálogo **nuevo proyecto** , haga clic en **varios**, desplácese hasta la sección **General** , seleccione la opción **aplicación móvil universal monogame** y haga clic en siguiente.

![Cuadro de diálogo nuevo proyecto que crea una aplicación de monogame](part1-images/image3.png)

Asigne al proyecto el nombre WalkingGame y haga clic en crear:

![Cuadro de diálogo nuevo proyecto que selecciona un nombre y una ubicación](part1-images/image4.png)

Ahora nuestro proyecto se ejecutará igual que cualquier otro proyecto de iOS o Android. El proyecto debe ejecutarse y mostrar un fondo azul azul aciano:

![Fondo en blanco de aplicación azul](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Corrección de errores de compilación de Android

La versión actual de las plantillas de monogame incluye algunos errores de sintaxis en el archivo de `Activity1.cs` de Android. Para solucionar estos problemas, reemplace la función `OnCreate` por la siguiente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Resumen

En este tutorial se ha explicado cómo crear un proyecto monogame multiplataforma mediante Visual Studio para Mac. El resultado de esta es una pantalla azul vacía. Este proyecto se puede usar como punto de partida para cualquier juego iOS y Android.

## <a name="related-links"></a>Vínculos relacionados

- [NuGet de Android de monogame](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [NuGet de iOS de monogame](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentación de la API de monogame](http://www.monogame.net/documentation/?page=main)
