---
title: Trabajar con barras de navegación de tvOS en Xamarin
description: En este documento se describe cómo trabajar con barras de navegación en una aplicación tvOS compilada con Xamarin. Describe cómo configurar las barras de navegación en un guión gráfico y responder a los eventos de estos botones.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 3ee159eaaf4a124f652b083e4dd7341909f52d82
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433120"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Trabajar con barras de navegación de tvOS en Xamarin

Las barras de navegación se pueden agregar a la parte superior de las vistas para mostrar un título y botones de barra de navegación opcionales. Normalmente se usan cuando el usuario ha navegado desde una página principal, como una vista de tabla, una colección o un menú a una vista, que muestra los detalles del elemento seleccionado.

[![Barra de navegación de ejemplo](navigation-bars-images/navbar01.png)](navigation-bars-images/navbar01.png#lightbox)

Además del título (que se muestra en el centro), las barras de navegación pueden contener uno o varios botones de barra de navegación ( `UIBarButtonItem` ) a la izquierda y a la derecha de la barra.

> [!IMPORTANT]
> Las barras de navegación son completamente transparentes de forma predeterminada. Se debe tener cuidado para asegurarse de que el contenido de la barra de navegación permanece legible sobre el contenido que se encuentra debajo de él. Por ejemplo, cuando el contenido de una vista de tabla o una colección se desplaza bajo él.

<a name="Navigation-Bars-and-Storyboards"></a>

## <a name="navigation-bars-and-storyboards"></a>Barras de navegación y guiones gráficos

La manera más sencilla de trabajar con barras de navegación en una aplicación Xamarin. tvOS es agregarlos a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en `Main.storyboard` archivo y ábralo para su edición.
1. Arrastre una **barra de navegación** desde el **cuadro de herramientas** y colóquela en la vista de la parte superior de la pantalla:

    [![Una barra de navegación](navigation-bars-images/navbar02.png)](navigation-bars-images/navbar02.png#lightbox)
1. Haga doble clic en la **barra de navegación** para seleccionar el **elemento de navegación**. En la pestaña **Widget** del **Panel de propiedades**, puede establecer el **título**:

    [![Establecer el título](navigation-bars-images/navbar03.png)](navigation-bars-images/navbar03.png#lightbox)
1. A continuación, puede agregar uno o varios **elementos de botón de barra** a cualquier extremo de la barra:

    [![Un elemento de botón de barra](navigation-bars-images/navbar04.png)](navigation-bars-images/navbar04.png#lightbox)
1. Por último, conecte los **elementos del botón de barra** a las acciones en la pestaña **eventos** del **Explorador de propiedades**:

    [![Una acción de elemento de botón de barra](navigation-bars-images/navbar05.png)](navigation-bars-images/navbar05.png#lightbox)
1. Guarde los cambios.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` archivo y ábralo para su edición.
1. Arrastre una **barra de navegación** desde el **cuadro de herramientas** y colóquela en la vista de la parte superior de la pantalla:

    [![Una barra de navegación](navigation-bars-images/navbar02-vs.png)](navigation-bars-images/navbar02-vs.png#lightbox)
1. Haga doble clic en la **barra de navegación** para seleccionar el **elemento de navegación**. En la pestaña **Widget** del **Explorador de propiedades**, puede establecer el **título**:

    [![Establecer el título](navigation-bars-images/navbar03-vs.png)](navigation-bars-images/navbar03-vs.png#lightbox)
1. A continuación, puede agregar uno o varios **elementos de botón de barra** a cualquier extremo de la barra:

    [![Elementos de botón de barra](navigation-bars-images/navbar04-vs.png)](navigation-bars-images/navbar04-vs.png#lightbox)
1. Por último, conecte los **elementos del botón de barra** a las acciones en la pestaña **eventos** del **Explorador de propiedades**:

    [![Acciones de un elemento de botón de barra](navigation-bars-images/navbar05-vs.png)](navigation-bars-images/navbar05-vs.png#lightbox)
1. Guarde los cambios.

-----

> [!IMPORTANT]
> Aunque es posible asignar eventos como `TouchUpInside` a un elemento de la interfaz de usuario (por ejemplo, un botón) en el diseñador de iOS, nunca se llamará porque Apple TV no tiene una pantalla táctil ni eventos táctiles de soporte técnico. Siempre debe usar el `Primary Action` evento al crear controladores de eventos para los elementos de la interfaz de usuario de tvOS.

El código siguiente proporciona un ejemplo de controladores de eventos en tres BarButtonItems diferentes: `ShowFirstHotel` , `ShowSecondHotel` y `ShowThirdHotel` . Cuando se hace clic en cada elemento, se cambia la imagen de fondo `HotelImage` . Esto se edita en el archivo de controlador de vista (ejemplo `ViewController.cs` ):

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

Siempre y cuando la propiedad de un botón `Enabled` sea `true` y no esté incluida en otro control o vista, se puede convertir en el elemento enfocado mediante Siri remoto.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con barras de navegación dentro de una aplicación Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)