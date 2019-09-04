---
title: Mejoras en el widget de pantalla principal y de búsqueda en iOS 10
description: En este documento se describen las mejoras que Apple ha realizado en los widgets de iOS 10, incluidas las actualizaciones para los widgets de búsqueda y de pantalla principal.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 71c58baee5e98efcfb38b75ffed2b85c90f8a131
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227365"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Mejoras en el widget de pantalla principal y de búsqueda en iOS 10

_En este artículo se describen las mejoras que Apple ha realizado en el sistema de widgets en iOS 10._

Apple ha introducido varias mejoras en el sistema de widgets para asegurarse de que los widgets tengan un aspecto excelente sobre cualquier fondo que exista en la nueva pantalla de bloqueo de iOS 10. Además, los widgets ahora contienen una propiedad [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) que permite al desarrollador describir la cantidad de contenido disponible y permite al usuario expandir y contraer el contenido.

Los widgets (también conocidos como extensiones de hoy) son un tipo especial de extensión de iOS que muestra una pequeña cantidad de información útil o expone la funcionalidad específica de la aplicación de manera oportuna. Por ejemplo, la aplicación News tiene un widget que muestra los titulares principales y la aplicación de calendario proporciona dos widgets distintos: uno para mostrar los eventos de hoy y otro para mostrar los próximos eventos.

Los widgets son muy personalizables y pueden contener elementos de interfaz de usuario como texto, imágenes, botones, etc. Además, el desarrollador puede personalizar aún más el diseño de sus widgets.

[![](widgets-images/widgets01.png "Widgets de ejemplo")](widgets-images/widgets01.png#lightbox)

Hay dos lugares principales que un usuario puede ver e interactuar con los widgets de una aplicación:

- **La pantalla de búsqueda** : los usuarios pueden agregar los widgets que buscan más útiles en la pantalla de búsqueda. Se tiene acceso a la pantalla de búsqueda al pasar el dedo hacia la derecha en las pantallas Inicio y bloqueo.
- **La pantalla principal** : en la pantalla principal, el usuario puede usar la función táctil 3D para abrir la lista de acciones rápidas aplicando presión al icono de la aplicación. Los widgets de una aplicación se mostrarán encima de la lista de acciones rápidas. Para obtener más información, consulte nuestra introducción a la documentación de [Touch 3D](~/ios/platform/3d-touch.md) .

## <a name="widgets-developer-suggestions"></a>Sugerencias para desarrolladores de widgets

Idealmente, el desarrollador siempre debe probar y diseñar widgets que el usuario desea agregar a sus pantallas de búsqueda. Para ello, Apple tiene las siguientes sugerencias:

- **Cree una experiencia excelente y de gran visión** : widgets de usuario que proporcionan información breve y de visión general de las actualizaciones de estado o permiten realizar tareas sencillas rápidamente. Esto hace que la cantidad adecuada de información e interactividad sea esencial. Siempre que sea posible, permita que el usuario realice una tarea determinada con un solo punteo. Además, como los widgets no admiten el movimiento panorámico o el desplazamiento, tendrá que tener en cuenta en el diseño del widget.
- **Mostrar rápidamente** los widgets de contenido están diseñados para ser de vista rápida, por lo que el usuario nunca debe esperar a que el contenido se cargue una vez que se muestra un widget. Los widgets deben almacenar en caché su contenido de forma local para que puedan mostrar el contenido reciente mientras el contenido nuevo se carga en segundo plano.
- **Proporcione el relleno y los márgenes adecuados** : los widgets nunca deben parecer amontonados, por lo que evite extender el contenido a los bordes de la vista de un widget. Siempre debe haber un margen ancho de varios píxeles entre los bordes y el contenido. Apple también sugiere el uso del icono de la aplicación, que se muestra en la parte superior del widget, como una guía de alineación. Si el widget presenta un diseño de cuadrícula, asegúrese de que haya un relleno adecuado entre los elementos de la cuadrícula e intente limitar el número de elementos a cuatro máx.
- **Usar diseños adaptables** : el ancho de un widget variará en función del dispositivo en el que se esté ejecutando y de la orientación del dispositivo. El alto de un widget también puede variar en función de si se muestra en un estado contraído (el valor predeterminado) o expandido (no compatible con todos los widgets). Un widget contraído tiene un alto de aproximadamente dos y medio de filas de tabla de iOS estándar. El desarrollador puede solicitar el tamaño de un widget expandido, pero es ideal que sea menor que el alto de la pantalla. En el estado contraído, el widget debería mostrar solo la información esencial e independiente. Cuando se expande, el widget debe mostrar información complementaria que mejora el contenido principal que se muestra en el estado contraído. Los widgets que se muestran en la lista de acciones rápidas solo estarán en el estado contraído.
- **No Personalice los widgets de fondo del widget** se muestran en un fondo claro y borroso proporcionado por el sistema. Esto se hace para fomentar la coherencia entre widgets y mejorar la legibilidad de su contenido. Evite el uso de una imagen como fondo del widget, ya que podría entrar en conflicto con los papeles tapiz de bloqueo y de pantalla principal del usuario.
- **Usar la fuente del sistema en color negro o gris oscuro** : cuando se muestra texto en un widget, la fuente del sistema funciona mejor. La fuente debe estar en color gris negro o oscuro para destacar el fondo de los widgets claros y borrosos.
- **Proporcionar acceso a las aplicaciones cuando** el widget correspondiente siempre debe funcionar por separado de la aplicación; sin embargo, si se requiere una funcionalidad más profunda, el widget debe ser capaz de iniciar la aplicación para ver o editar una parte específica de la información. No incluya nunca un botón "abrir aplicación", simplemente permita que el usuario pulse en el propio contenido y nunca abra una aplicación de terceros.
- **Seleccione un nombre de widget claro y conciso** : el icono de la aplicación y el nombre del widget siempre se muestran en el contenido del widget. Apple sugiere el uso del nombre de la aplicación para su widget principal y un nombre claro y conciso para cualquier otro que proporcione. Al proporcionar un título de widget personalizado, debe prefijarse con el nombre de la aplicación (por ejemplo, mapas cercanos, mapas, etc.).
- Informar de si la **autenticación agrega valor** , si la funcionalidad o información adicional solo está disponible cuando el usuario se autentica y se inicia sesión, preséntela al usuario. Por ejemplo, una aplicación de uso compartido de conducción puede indicar "iniciar sesión en el inicio del libro".
- **Seleccionar un widget de lista de acciones rápidas** : Si la aplicación proporciona más de un widget, el desarrollador debe elegir el que se va a presentar cuando el usuario muestre la lista de acciones rápidas aplicando presión al icono de la aplicación mediante el toque 3D.

Para más información sobre cómo trabajar con widgets, consulte la [Introducción a las extensiones](~/ios/platform/extensions.md), introducción a la documentación de [Touch 3D](~/ios/platform/3d-touch.md) y guía de programación de la [extensión de aplicaciones](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)de Apple.

## <a name="working-with-vibrancy"></a>Trabajar con vibrancy

Vibrancy garantiza que el texto de un widget siga siendo legible cuando se presente en el fondo borroso del widget (proporcionado por el sistema). Antes de iOS 10, el desarrollador usaría un [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) para el vibrancy del widget. Por ejemplo:

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Esto está en desuso en iOS 10 y debe reemplazarse por un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o un [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Por ejemplo:

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Trabajar con widgets contraídos y expandidos

Como novedad en iOS 10, los widgets ahora contienen una propiedad [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) que permite al desarrollador describir la cantidad de contenido disponible y permite que el usuario expanda y contraiga el contenido.

Cuando se muestra inicialmente un widget, está en un estado contraído. Un widget contraído tiene un alto de aproximadamente dos y medio de filas de tabla de iOS estándar. El desarrollador puede solicitar el tamaño de un widget expandido, pero es ideal que sea menor que el alto de la pantalla.

En el estado contraído, el widget debería mostrar solo la información esencial e independiente. Cuando se expande, el widget debe mostrar información complementaria que mejora el contenido principal que se muestra en el estado contraído. Por ejemplo, la aplicación Weather muestra las condiciones meteorológicas actuales cuando están contraídas y agrega la previsión por hora cuando se expande.

Los widgets que se muestran en la lista de acciones rápidas solo estarán en el estado contraído. Si la aplicación proporciona más de un widget, el desarrollador debe elegir el que se va a presentar cuando el usuario muestre la lista de acciones rápidas aplicando presión al icono de la aplicación mediante el toque 3D.

El ejemplo siguiente es de una extensión de hoy (widget) simple que controla los Estados contraído y expandido:

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

Eche un vistazo al código específico del modo de presentación del widget en detalle. Para informar al sistema de que este widget es compatible con el estado expandido, usa:

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Para obtener el modo de presentación actual del widget, se usa:

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Para obtener el tamaño máximo para el estado contraído o expandido, usa:

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

Y para controlar el cambio de estado (modo de presentación), usa:

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

Además de establecer el tamaño solicitado para cada Estado (contraído o expandido), también actualiza el contenido que se muestra para que coincida con el nuevo tamaño.

## <a name="summary"></a>Resumen

En este artículo se han explicado las mejoras que Apple ha realizado en el sistema de widgets en iOS 10 y se ha mostrado cómo implementarlas en Xamarin. iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Introducción a las extensiones](~/ios/platform/extensions.md)
- [Introducción a la función táctil 3D](~/ios/platform/3d-touch.md)
- [Guía de programación de extensiones de aplicaciones](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)
