---
title: Trabajar con controles de página de tvOS en Xamarin
description: En este documento se describe cómo trabajar con controles de página de tvOS en una aplicación compilada con Xamarin. Proporciona una descripción de alto nivel de los controles de página, explica cómo configurarlos en guiones gráficos y examina cómo responder a los eventos de cambio de página.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: ec4b99fd1c8ec28f6b4bbb30695613da6c40d60c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288949"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Trabajar con controles de página de tvOS en Xamarin

En ocasiones, es posible que necesite mostrar una serie de páginas o imágenes en la aplicación Xamarin. tvOS. Un control de página se diseñó para mostrar con claridad qué página de un usuario está fuera del número máximo de páginas. Un control de página muestra una serie de puntos de un fondo oscuro y con forma de óvalo. La página actual mostrará un punto relleno, todas las demás páginas se muestran como puntos huecos. El control de página recortará los puntos más externos si hay demasiados para caber en su área de fondo.

[![](page-controls-images/page01.png "Control de página de ejemplo")](page-controls-images/page01.png#lightbox)

Control de página en un elemento no interactivo diseñado para proporcionar comentarios solo al usuario. Tendrá que agregar otros controles para cambiar el número de página actual (por ejemplo, gestos o botones).

Apple tiene las siguientes sugerencias cuando se usa un control de página:

- **Usar solo en colecciones completas** : los controles de página funcionan mejor en un entorno de pantalla completa para mostrar varias páginas que existen en una sola colección.
- **Limite el número de páginas** : los controles de página funcionan mejor para diez (10) o menos páginas, y un máximo de veinte (20) páginas. Para más de veinte páginas, considere la posibilidad de usar una [vista de colección](~/ios/tvos/user-interface/collection-views.md) y mostrar las páginas en una cuadrícula.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Controles de página y guiones gráficos

La manera más sencilla de trabajar con controles de página en una aplicación Xamarin. tvOS es agregarlos a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


1. En el **Panel de solución**, haga doble clic en `Main.storyboard` el archivo y ábralo para su edición.
1. Arrastre un **control de página** desde el **cuadro de herramientas** y colóquelo en la vista:

    [![](page-controls-images/page02.png "Un control de página")](page-controls-images/page02.png#lightbox)
1. En la **pestaña widget** del **Panel de propiedades**, puede ajustar varias propiedades del control de página, como la **Página actual** y el **número de páginas**:

    [![](page-controls-images/page03.png "La pestaña widget")](page-controls-images/page03.png#lightbox)
1. A continuación, agregue controles o gestos a la vista para moverse hacia atrás y hacia delante por la colección de páginas.
1. Por último, asigne **nombres** a los controles para que pueda responder a ellos en C# el código. Por ejemplo:

    [![](page-controls-images/page04.png "Asignar nombre al control")](page-controls-images/page04.png#lightbox)
1. Guarde los cambios.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` el archivo y ábralo para su edición.
1. Arrastre un **control de página** desde el **cuadro de herramientas** y colóquelo en la vista:

    [![](page-controls-images/page02-vs.png "Un control de página")](page-controls-images/page02-vs.png#lightbox)
1. En la **pestaña widget** del **Explorador de propiedades**, puede ajustar varias propiedades del control de página, como la **Página actual** y el **número de páginas**:

    [![](page-controls-images/page03-vs.png "La pestaña widget")](page-controls-images/page03-vs.png#lightbox)
1. A continuación, agregue controles o gestos a la vista para moverse hacia atrás y hacia delante por la colección de páginas.
1. Por último, asigne **nombres** a los controles para que pueda responder a ellos en C# el código. Por ejemplo:

    [![](page-controls-images/page04-vs.png "Asignar nombre al control")](page-controls-images/page04-vs.png#lightbox)
1. Guarde los cambios.


-----

> [!IMPORTANT]
> Aunque es posible asignar eventos como `TouchUpInside` a un elemento de la interfaz de usuario (por ejemplo, un botón) en el diseñador de iOS, nunca se llamará porque Apple TV no tiene una pantalla táctil ni eventos táctiles de soporte técnico. Siempre debe usar el `Primary Action` evento al crear controladores de eventos para los elementos de la interfaz de usuario de tvOS.

Edite el archivo del controlador `ViewController.cs`de vista (ejemplo) y agregue el código para controlar las páginas que se están cambiando. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

Echemos un vistazo más de cerca a dos propiedades del control de página. En primer lugar, para especificar el número máximo de páginas, use lo siguiente:

```csharp
PageView.Pages = 6;
```

Para cambiar el número de página actual, use el siguiente código:

```csharp
PageView.CurrentPage = PageNumber;
```

La `CurrentPage` propiedad es cero (0), por lo que la primera página será cero y la última será una menos el número máximo de páginas.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con el control de página dentro de una aplicación Xamarin. tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
