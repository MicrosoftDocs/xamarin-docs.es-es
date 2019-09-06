---
title: Introducción a la función táctil 3D en Xamarin. iOS
description: En este artículo se describe cómo usar los gestos táctiles 3D introducidos con iPhone 6S y iPhone 6S Plus. Estos gestos permiten la sensibilidad a la presión, los elementos PEEK y pop, y las acciones rápidas.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 46db28dab32f14fa476b9fbb42b788feb669aa74
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291864"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introducción a la función táctil 3D en Xamarin. iOS

_En este artículo se explica el uso de los nuevos movimientos de iPhone 6S y iPhone 6S Plus 3D Touch en la aplicación._

[![](3d-touch-images/info01.png "Ejemplos de aplicaciones habilitadas para toque 3D")](3d-touch-images/info01.png#lightbox)

En este artículo se proporciona una introducción al uso de las nuevas API de toque 3D para agregar gestos sensibles a la presión a las aplicaciones de Xamarin. iOS que se ejecutan en los nuevos dispositivos iPhone 6S y iPhone 6S Plus.

Con la función táctil 3D, una aplicación de iPhone ahora es capaz de no solo indicar que el usuario está tocando la pantalla del dispositivo, pero es capaz de detectar cuánta presión está ejerciendo el usuario y responder a los diferentes niveles de presión.

en 3D Touch se proporcionan las siguientes características a la aplicación:

- [Sensibilidad](#Pressure-Sensitivity) a la presión: ahora, las aplicaciones pueden medir el grado de dificultad o luz que el usuario toca con la pantalla y aprovechar esa información.
  Por ejemplo, una aplicación de dibujo puede hacer que una línea sea más gruesa o más delgada en función de cómo toque el usuario a la pantalla.
- [PEEK y pop](#Peek-and-Pop) : la aplicación ahora puede permitir que el usuario interactúe con sus datos sin tener que navegar fuera de su contexto actual. Al presionar el dedo en la pantalla, puede ver el elemento que le interesan (por ejemplo, al obtener una vista previa de un mensaje). Al presionar más duro, pueden entrar en el elemento.
- [Acciones rápidas](#Quick-Actions) : Piense en acciones rápidas como los menús contextuales que se pueden desplegar cuando un usuario hace clic con el botón derecho en un elemento de una aplicación de escritorio.
  Con las acciones rápidas, puede agregar accesos directos a las funciones de la aplicación directamente desde el icono de la aplicación en la pantalla principal.
- [Prueba de la función táctil 3D en el simulador](#Testing-3D-Touch-in-the-Simulator) : con el hardware de Mac correcto, puede probar aplicaciones 3D táctiles habilitadas en el simulador de iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Sensibilidad a la presión

Como se indicó anteriormente, mediante el uso de nuevas propiedades de la clase [UITouch](xref:UIKit.UITouch) , puede medir la cantidad de presión que el usuario aplica a la pantalla del dispositivo iOS y usar esta información en la interfaz de usuario. Por ejemplo, crear un trazo de pincel más translúcido u opaco en función de la cantidad de presión.

[![](3d-touch-images/pressure01.png "Trazo de pincel representado como más traslúcido o opaco según la cantidad de presión")](3d-touch-images/pressure01.png#lightbox)

Como resultado de la función táctil 3D, si la aplicación se ejecuta en iOS 9 (o posterior) y el dispositivo iOS es capaz de admitir la funcionalidad táctil 3D, los cambios en `TouchesMoved` la presión harán que se genere el evento.

Por ejemplo, al supervisar el `TouchesMoved` evento de un [UIView](xref:UIKit.UIView), puede usar el código siguiente para obtener la presión actual que el usuario aplica a la pantalla:

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

La `MaximumPossibleForce` propiedad devuelve el valor más alto posible para `Force` la propiedad de [UITouch](xref:UIKit.UITouch) basándose en el dispositivo iOS en el que se ejecuta la aplicación.

> [!IMPORTANT]
> Los cambios de presión harán que `TouchesMoved` se produzca el evento, incluso si las coordenadas X/y no han cambiado. Debido a este cambio de comportamiento, las aplicaciones de iOS deberían estar preparadas para que el `TouchesMoved` evento se invoque con más frecuencia y para que las coordenadas X/y sean iguales que la última `TouchesMoved` llamada.




Para obtener más información, consulte TouchCanvas de [Apple: Uso de UITouch de aplicación](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) de ejemplo eficaz y eficaz y [referencia de clase UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Inspeccionar y extraer

la función táctil 3D proporciona nuevas formas de que un usuario interactúe con la información de su aplicación más rápido que nunca, sin tener que navegar desde su ubicación actual.

Por ejemplo, si la aplicación muestra una tabla de mensajes, el usuario puede pulsar en un elemento para obtener una vista previa de su contenido en una vista de superposición (a la que Apple hace referencia como un *vistazo*).

[![](3d-touch-images/peekandpop01.png "Ejemplo de cómo inspeccionar el contenido")](3d-touch-images/peekandpop01.png#lightbox)

Si el usuario presiona más duro, entrará en la vista normal de mensajes (lo que se conoce *como ping en la*vista).

### <a name="checking-for-3d-touch-availability"></a>Comprobar la disponibilidad de la función táctil 3D

Al trabajar con `UIViewController` , puede usar el código siguiente para ver si el dispositivo iOS en el que se ejecuta la aplicación admite el toque 3D:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

Se puede llamar a este método antes *o después* `ViewDidLoad()`de.

### <a name="handling-peek-and-pop"></a>Control de PEEK y pop

En un dispositivo iOS que puede controlar el toque 3D, podemos usar una instancia de la `UIViewControllerPreviewingDelegate` clase para controlar la presentación de los detalles de los elementos **PEEK** y **pop** . Por ejemplo, si tenemos un controlador de vista de tabla `MasterViewController` llamado, podríamos usar el código siguiente para admitir **PEEK** y **pop**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

El `GetViewControllerForPreview` método se usa para realizar la operación de **lectura** . Obtiene acceso a la celda de la tabla y a los datos `DetailViewController` de respaldo y, a continuación, carga desde el guión gráfico actual. Al establecer `PreferredContentSize` en (0,0), se solicita el tamaño predeterminado de la vista de **búsqueda** . Por último, se desenfoca todo excepto la celda que se `previewingContext.SourceRect = cell.Frame` muestra con y se devuelve la nueva vista para mostrarla.

Vuelve a usar la vista que creamos en la vista de **pop** cuando el usuario presiona más difícil. `CommitViewController`

### <a name="registering-for-peek-and-pop"></a>Registro para PEEK y pop

En el controlador de vista en el que queremos **permitir que el usuario examine y examine** **los elementos,** es necesario registrarse para este servicio. En el ejemplo anterior de un controlador de vista de tabla`MasterViewController`(), se usaría el siguiente código:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Aquí vamos a llamar al `RegisterForPreviewingWithDelegate` método con una instancia `PreviewingDelegate` de creada anteriormente. En los dispositivos iOS que admiten la función táctil 3D, el usuario puede pulsar en un elemento para leerlo. Si presionan incluso más difíciles, el elemento se mostrará en la vista estándar.

Para obtener más información, consulte el [ejemplo de ApplicationShortcuts de iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) y [ViewControllerPreviews de Apple: Usar la aplicación de ejemplo de API](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) de vista previa de UIViewController, [referencia de clase UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), referencia de [clase UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) y [referencia de protocolo UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Acciones rápidas

Con las acciones rápidas y táctiles 3D, puede agregar accesos directos comunes y rápidos y fáciles de acceder a las funciones de la aplicación desde el icono de la pantalla principal del dispositivo iOS.

Como se indicó anteriormente, puede considerar acciones rápidas como los menús contextuales que se pueden desplegar cuando un usuario hace clic con el botón derecho en un elemento de una aplicación de escritorio. Debe usar acciones rápidas para proporcionar accesos directos a las funciones o características más comunes de la aplicación.

[![](3d-touch-images/quickactions01.png "Un ejemplo de un menú de acciones rápidas")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Definir acciones rápidas estáticas

Si una o varias de las acciones rápidas que requiere la aplicación son estáticas y no necesita cambiar, puede definirlas en el archivo de `Info.plist` la aplicación. Edite este archivo en un editor externo y agregue las claves siguientes:

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

Aquí vamos a definir dos elementos de acción rápida estáticos con las claves siguientes:

- `UIApplicationShortcutItemIconType`: Define el icono que se mostrará en el elemento de acción rápida como uno de los siguientes valores:
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

  ![](3d-touch-images/uiapplicationshortcuticontype.png "Imagen UIApplicationShortcutIconType")

- `UIApplicationShortcutItemSubtitle`: Define el subtítulo del elemento.
- `UIApplicationShortcutItemTitle`: Define el título del elemento.
- `UIApplicationShortcutItemType`: Es un valor de cadena que se va a usar para identificar el elemento en la aplicación. Para obtener más información, vea la sección siguiente.

> [!IMPORTANT]
> No se puede tener acceso a los elementos de `Info.plist` acceso directo de acción rápida que se `Application.ShortcutItems` establecen en el archivo con la propiedad. Solo se pasan al `HandleShortcutItem` controlador de eventos.





### <a name="identifying-quick-action-items"></a>Identificar elementos de acción rápida

Como vimos anteriormente, al definir los elementos de acción rápida en la aplicación `Info.plist`, ha asignado un valor de cadena a la `UIApplicationShortcutItemType` clave para identificarlos.

Para facilitar el trabajo con estos identificadores en el código, agregue una clase denominada `ShortcutIdentifier` al proyecto de la aplicación y haga que tenga un aspecto similar al siguiente:

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>Controlar una acción rápida

A continuación, debe modificar el archivo de `AppDelegate.cs` la aplicación para que el usuario seleccione un elemento de acción rápida del icono de la aplicación en la pantalla principal.

Realice las siguientes modificaciones:

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

En primer lugar, se define `LaunchedShortcutItem` una propiedad pública para realizar el seguimiento del último elemento de acción rápida seleccionado por el usuario. A continuación, se invalida `FinishedLaunching` el método y se `launchOptions` ve si se han pasado y si contienen un elemento de acción rápida. Si lo hacen, se almacena la acción rápida en la `LaunchedShortcutItem` propiedad.

Después, invalide `OnActivated` el método y pase cualquier elemento de inicio rápido seleccionado `HandleShortcutItem` al método en el que se va a actuar. Actualmente solo estamos escribiendo un mensaje en la **consola**. En una aplicación real, podría controlar qué acción fue necesaria. Una vez realizada la acción, se borra `LaunchedShortcutItem` la propiedad.

Por último, si la aplicación ya se estaba ejecutando `PerformActionForShortcutItem` , se llamaría al método para controlar el elemento de acción rápida, por lo que necesitamos reemplazarlo `HandleShortcutItem` y llamar a nuestro método aquí también.


### <a name="creating-dynamic-quick-action-items"></a>Crear elementos de acción rápida dinámica

Además de definir elementos de acción rápida estáticos en el archivo `Info.plist` de la aplicación, puede crear acciones rápidas dinámicas sobre la marcha. Para definir dos nuevas acciones rápidas dinámicas, edite `AppDelegate.cs` el archivo de nuevo y modifique el `FinishedLaunching` método para que tenga un aspecto similar al siguiente:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

Ahora estamos comprobando para ver si el `application` ya contiene un conjunto de objetos creados `ShortcutItems`dinámicamente, si no vamos a crear dos nuevos `UIMutableApplicationShortcutItem` objetos para definir los nuevos elementos y agregarlos a la `ShortcutItems` matriz.

El código que ya hemos agregado en la sección [control de una acción rápida](#Handling-a-Quick-Action) anterior controlará estas acciones rápidas dinámicas, al igual que las estáticas.

Debe tenerse en contada que puede crear una mezcla de elementos de acción rápida estáticos y dinámicos (como hacemos aquí), no está limitado a uno u otro.

Para obtener más información, consulte nuestro [ejemplo de ViewControllerPreview de iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) y [vea ApplicationShortcuts de Apple: Uso de](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) la aplicación de ejemplo UIApplicationShortcutItem, [referencia de clase UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), referencia de clase [UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) y [referencia de clase UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Probar la función táctil 3D en el simulador

Al usar la versión más reciente de Xcode y el simulador de iOS en un equipo Mac compatible con una Force Touch habilitar el control de pantalla, puede probar la funcionalidad táctil 3D en el simulador.

Para habilitar esta funcionalidad, ejecute cualquier aplicación en hardware de iPhone simulado que admita el toque 3D (iPhone 6S y versiones posteriores). A continuación, seleccione el menú de **hardware** en el simulador de iOS y habilite el elemento de menú **usar el fuerza de pantalla para el toque 3D** :

[![](3d-touch-images/simulator01.png "Seleccione el menú de hardware en el simulador de iOS y habilite el elemento de menú usar el fuerza de pantalla para el toque 3D.")](3d-touch-images/simulator01.png#lightbox)

Con esta característica activa, puede presionar más fuerte en el toques del equipo Mac para habilitar la función táctil 3D, al igual que haría en el hardware de iPhone real.

## <a name="summary"></a>Resumen

En este artículo se han incorporado las nuevas API táctiles 3D que están disponibles en iOS 9 para iPhone 6S y iPhone 6S Plus. Se ha tratado la adición de sensibilidad a la presión a una aplicación; usar PEEK y pop para mostrar rápidamente información de la aplicación desde el contexto actual sin navegación; y el uso de acciones rápidas para proporcionar accesos directos a las características de uso más frecuente de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de ViewControllerPreview de iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [Ejemplo de ApplicationShortcuts de iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Preparar las aplicaciones de iPhone para la funcionalidad táctil 3D](https://developer.apple.com/ios/3d-touch/)
