---
title: Extensiones de iOS en Xamarin. iOS
description: En este documento se describen las extensiones, que son widgets presentados por iOS en el contexto estándar, como en el centro de notificaciones. Describe cómo crear una extensión y comunicarse con ella desde la aplicación primaria.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 0cf44a05f8b40a07dcc099d5789171f4a234a0c2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032572"
---
# <a name="ios-extensions-in-xamarinios"></a>extensiones de iOS en Xamarin. iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Crear extensiones en vídeo de iOS**

Las extensiones, como se introdujeron en iOS 8, están especializadas `UIViewControllers` presentadas por iOS dentro de contextos estándar, como en el **centro de notificaciones**, como tipos de teclado personalizados solicitados por el usuario para realizar entradas especializadas u otros contextos como editar una fotografía en la que la extensión puede proporcionar filtros de efectos especiales.

Todas las extensiones se instalan junto con una aplicación de contenedor (con ambos elementos escritos mediante las API unificadas de 64 bits) y se activan desde un punto de extensión determinado en una aplicación host. Y dado que se usarán como complementos para las funciones del sistema existentes, deben ser de alto rendimiento, eficaces y robustos. 

## <a name="extension-points"></a>Puntos de extensión

|Type|Descripción|Punto de extensión|Aplicación host|
|--- |--- |--- |--- |
|Acción|Editor o visor especializado para un tipo de medio determinado|`com.apple.ui-services`|Cualquiera|
|Proveedor de documentos|Permite que la aplicación use un almacén de documentos remoto|`com.apple.fileprovider-ui`|Aplicaciones que usan un [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Cualquiera|
|Edición de fotografías|Manipulación y edición de fotografías|`com.apple.photo-editing`|Editor de fotos. app|
|Compartir|Comparte datos con redes sociales, servicios de mensajería, etc.|`com.apple.share-services`|Cualquiera|
|Hoy|"Widgets" que aparecen en la pantalla de hoy o en el centro de notificaciones|`com.apple.widget-extensions`|Hoy y centro de notificaciones|

Se han agregado [puntos de extensión adicionales](~/ios/platform/introduction-to-ios10/index.md#app-extensions) en iOS 10.

## <a name="limitations"></a>Limitaciones

Las extensiones tienen una serie de limitaciones, algunas de las cuales son universales para todos los tipos (por ejemplo, ningún tipo de extensión puede tener acceso a las cámaras o los micrófonos), mientras que otros tipos de extensión pueden tener limitaciones específicas en su uso (por ejemplo, teclados personalizados no se puede usar para los campos de entrada de datos seguros, como las contraseñas. 

Las limitaciones universales son:

- El [Kit de mantenimiento](~/ios/platform/healthkit.md) y los marcos de [interfaz de usuario del kit de eventos](~/ios/platform/eventkit.md) no están disponibles
- Las extensiones no pueden usar [modos en segundo plano extendidos](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- Las extensiones no pueden tener acceso a las cámaras o micrófonos del dispositivo (aunque pueden acceder a archivos multimedia existentes)
- Las extensiones no pueden recibir datos de colocación de aire (aunque pueden transmitir datos mediante Air Drop)
- [UIActionSheet](xref:UIKit.UIActionSheet) y [UIAlertView](xref:UIKit.UIAlertView) no están disponibles; las extensiones deben usar [UIAlertController](xref:UIKit.UIAlertController)
- Varios miembros de [UIApplication](xref:UIKit.UIApplication) no están disponibles: [UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication. OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication. BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) y [ UIApplication. EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS exige un límite de uso de memoria de 16 MB en las extensiones de hoy.
- De forma predeterminada, las extensiones de teclado no tienen acceso a la red. Esto afecta a la depuración en el dispositivo (la restricción no se aplica en el simulador), ya que Xamarin. iOS requiere acceso a la red para que la depuración funcione. Es posible solicitar el acceso a la red estableciendo el valor de `Requests Open Access` en info. plist del proyecto en `Yes`. Consulte la guía de [teclado personalizada](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) de Apple para obtener más información sobre las limitaciones de la extensión del teclado.

Para conocer las limitaciones individuales, consulte la [Guía de programación](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)de la extensión de aplicaciones de Apple.

## <a name="distributing-installing-and-running-extensions"></a>Distribuir, instalar y ejecutar extensiones

Las extensiones se distribuyen desde una aplicación de contenedor, que, a su vez, se envía y se distribuye a través de la tienda de aplicaciones. La extensión (s) distribuida con la aplicación se instala en ese momento, pero el usuario debe habilitar cada extensión explícitamente. Los diferentes tipos de extensiones se habilitan de maneras diferentes; algunos requieren que el usuario navegue hasta la aplicación de **configuración** y los habilite desde allí. Mientras que otras se habilitan en el punto de uso, como la habilitación de una extensión de uso compartido al enviar una foto. 

La aplicación en la que se usa la extensión (donde el usuario encuentra el punto de extensión) se conoce como la **aplicación host**, ya que es la aplicación que hospeda la extensión cuando se ejecuta. La aplicación que instala la extensión es la **aplicación contenedora**, ya que es la aplicación que contenía la extensión cuando se instaló.  

Normalmente, la aplicación contenedora describe la extensión y dirige al usuario a través del proceso de habilitación.

## <a name="extension-lifecycle"></a>Ciclo de vida de la extensión

Una extensión puede ser tan simple como una sola [UIViewController](xref:UIKit.UIViewController) o extensiones más complejas que presenten varias pantallas de interfaz de usuario. Cuando el usuario encuentra un punto de _extensión_ (por ejemplo, al compartir una imagen), tendrá la oportunidad de elegir entre las extensiones registradas para ese punto de extensión. 

Si eligen una de las extensiones de la aplicación, se crearán instancias de sus `UIViewController` y comenzarán el ciclo de vida de los controladores de vista normales. Sin embargo, a diferencia de una aplicación normal, que están suspendidas pero no suelen terminar cuando el usuario termina de interactuar con ellas, las extensiones se cargan, ejecutan y finalizan repetidamente.

Las extensiones pueden comunicarse con sus aplicaciones host a través de un objeto [NSExtensionContext](xref:Foundation.NSExtensionContext) . Algunas extensiones tienen operaciones que reciben devoluciones de llamada asincrónicas con los resultados. Estas devoluciones de llamada se ejecutarán en subprocesos en segundo plano y la extensión debe tener esto en cuenta. por ejemplo, mediante [NSObject. InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*) si desean actualizar la interfaz de usuario. Consulte la sección [comunicación con la aplicación host](#communicating-with-the-host-app) a continuación para más información.

De forma predeterminada, las extensiones y sus aplicaciones de contenedor no se pueden comunicar, a pesar de que se instalan juntas. En algunos casos, la aplicación contenedora es esencialmente un contenedor de "envío" vacío cuyo propósito se atiende una vez que se instala la extensión. Sin embargo, si las circunstancias dictan, la aplicación contenedora y la extensión pueden compartir recursos desde un área común. Además, una **extensión de hoy** puede solicitar que su aplicación contenedora abra una dirección URL. Este comportamiento se muestra en el [Widget cuenta atrás de eventos](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions).

## <a name="creating-an-extension"></a>Crear una extensión

Las extensiones (y sus aplicaciones contenedoras) deben ser binarios de 64 bits y compilarse con las [API unificadas](~/cross-platform/macios/unified/index.md)de Xamarin. iOS. Al desarrollar una extensión, las soluciones contendrán al menos dos proyectos: la aplicación contenedora y un proyecto para cada extensión que el contenedor proporcione.

### <a name="container-app-project-requirements"></a>Requisitos del proyecto de aplicación de contenedor

La aplicación contenedora que se usa para instalar la extensión tiene los siguientes requisitos:

- Debe mantener una referencia al proyecto de extensión.   
- Debe ser una aplicación completa (debe ser capaz de iniciarse y ejecutarse correctamente) incluso si no hace nada más que proporcionar una manera de instalar una extensión. 
- Debe tener un identificador de paquete que sea la base del identificador de paquete del proyecto de extensión (consulte la sección siguiente para obtener más detalles).

### <a name="extension-project-requirements"></a>Requisitos del proyecto de extensión

Además, el proyecto de la extensión tiene los siguientes requisitos:

- Debe tener un identificador de paquete que empiece por el identificador de lote de la aplicación contenedora. Por ejemplo, si la aplicación contenedora tiene un identificador de paquete de `com.myCompany.ContainerApp`, el identificador de la extensión podría ser `com.myCompany.ContainerApp.MyExtension`: 

  ![](extensions-images/bundleidentifiers.png) 
- Debe definir la clave `NSExtensionPointIdentifier`, con un valor adecuado (como `com.apple.widget-extension` para un widget del centro de notificaciones de **hoy en día** ), en su archivo `Info.plist`.
- También debe definir la clave de `NSExtensionMainStoryboard` *o la clave* de `NSExtensionPrincipalClass` en su archivo `Info.plist` con un valor adecuado:
  - Use la clave `NSExtensionMainStoryboard` para especificar el nombre del guion gráfico que presenta la interfaz de usuario principal para la extensión (menos `.storyboard`). Por ejemplo, `Main` para el archivo de `Main.storyboard`.
  - Use la clave `NSExtensionPrincipalClass` para especificar la clase que se inicializará cuando se inicie la extensión. El valor debe coincidir con el valor de **registro** del `UIViewController`: 

  ![](extensions-images/registerandprincipalclass.png)

Los tipos específicos de extensiones pueden tener requisitos adicionales. Por ejemplo, **hoy** o la clase principal de la extensión del **centro de notificaciones** debe implementar [INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding).

> [!IMPORTANT]
> Si inicia el proyecto mediante una de las plantillas de extensiones proporcionadas por Visual Studio para Mac, la mayoría de los requisitos (si no todos) se proporcionarán y se cumplirán automáticamente con la plantilla.

## <a name="walkthrough"></a>Tutorial 

En el siguiente tutorial, creará un widget de ejemplo **hoy** en el que se calcula el día y el número de días restantes del año:

[![](extensions-images/carpediemscreenshot-sm.png "An example Today widget that calculates the day and number of days remaining in the year")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Crear la solución

Para crear la solución necesaria, haga lo siguiente:

1. En primer lugar, cree un nuevo proyecto de **aplicación de vista única** de iOS y haga clic en el botón **siguiente** : 

    [![](extensions-images/today01.png "First, create a new iOS, Single View App project and click the Next button")](extensions-images/today01.png#lightbox)
2. Llame al `TodayContainer` del proyecto y haga clic en el botón **siguiente** : 

    [![](extensions-images/today02.png "Call the project TodayContainer and click the Next button")](extensions-images/today02.png#lightbox)
3. Compruebe el **nombre del proyecto** y el **nombredesolución** y haga clic en el botón **crear** para crear la solución: 

    [![](extensions-images/today03.png "Verify the Project Name and SolutionName and click the Create button to create the solution")](extensions-images/today03.png#lightbox)
4. Después, en el **Explorador de soluciones**, haga clic con el botón derecho en la solución y agregue un nuevo proyecto de **extensión de iOS** a partir de la plantilla de **extensión de hoy** : 

    [![](extensions-images/today04.png "Next, in the Solution Explorer, right-click on the Solution and add a new iOS Extension project from the Today Extension template")](extensions-images/today04.png#lightbox)
5. Llame al `DaysRemaining` del proyecto y haga clic en el botón **siguiente** : 

    [![](extensions-images/today05.png "Call the project DaysRemaining and click the Next button")](extensions-images/today05.png#lightbox)
6. Revise el proyecto y haga clic en el botón **crear** para crearlo: 

    [![](extensions-images/today06.png "Review the project and click the Create button to create it")](extensions-images/today06.png#lightbox)

La solución resultante debería tener ahora dos proyectos, como se muestra aquí:

[![](extensions-images/today07.png "The resulting Solution should now have two projects, as shown here")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Crear la interfaz de usuario de la extensión

A continuación, tendrá que diseñar la interfaz para el widget **hoy** . Esto puede hacerse mediante un guión gráfico o mediante la creación de la interfaz de usuario en el código. Ambos métodos se tratarán con más detalle.

#### <a name="using-storyboards"></a>Usar guiones gráficos

Para compilar la interfaz de usuario con un guion gráfico, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo de `Main.storyboard` del proyecto de extensión para abrirlo para su edición: 

    [![](extensions-images/today08.png "Double-click the Extension projects Main.storyboard file to open it for editing")](extensions-images/today08.png#lightbox)
2. Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario por plantilla y asígnele el **nombre** `TodayMessage` en la pestaña **Widget** del **Explorador de propiedades**: 

    [![](extensions-images/today09.png "Select the Label that was automatically added to the UI by template and give it the Name TodayMessage in the Widget tab of the Properties Explorer")](extensions-images/today09.png#lightbox)
3. Guarde los cambios en el guion gráfico.

#### <a name="using-code"></a>Usar código

Para compilar la interfaz de usuario en el código, haga lo siguiente: 

1. En el **Explorador de soluciones**, seleccione el proyecto **DaysRemaining** , agregue una nueva clase y llámela `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect the DaysRemaining project, add a new class and call it CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. De nuevo, en el **Explorador de soluciones**, haga doble clic en el archivo de `Info.plist` de la extensión para abrirlo para su edición: 

    [![](extensions-images/code02.png "Double-click Extensions Info.plist file to open it for editing")](extensions-images/code02.png#lightbox)
3. Seleccione la **vista Código fuente** (en la parte inferior de la pantalla) y abra el nodo `NSExtension`: 

    [![](extensions-images/code03.png "Select the Source View from the bottom of the screen and open the NSExtension node")](extensions-images/code03.png#lightbox)
4. Quite la clave `NSExtensionMainStoryboard` y agregue una `NSExtensionPrincipalClass` con el valor `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Remove the NSExtensionMainStoryboard key and add a NSExtensionPrincipalClass with the value CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Guarde los cambios.

A continuación, edite el archivo de `CodeBasedViewController.cs` y haga que tenga el aspecto siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
  [Register("CodeBasedViewController")]
  public class CodeBasedViewController : UIViewController, INCWidgetProviding
  {
    public CodeBasedViewController ()
    {
    }

    public override void ViewDidLoad ()
    {
      base.ViewDidLoad ();

      // Add label to view
      var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
        TextAlignment = UITextAlignment.Center
      };

      View.AddSubview (TodayMessage);

      // Insert code to power extension here...

    }
  }
}
```

Tenga en cuenta que la `[Register("CodeBasedViewController")]` coincide con el valor especificado para el `NSExtensionPrincipalClass` anterior.

### <a name="coding-the-extension"></a>Codificación de la extensión

Con la interfaz de usuario creada, abra el `TodayViewController.cs` o el archivo de `CodeBasedViewController.cs` (basado en el método utilizado para crear la interfaz de usuario anterior), cambie el método **ViewDidLoad** y haga que tenga un aspecto similar al siguiente:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  // Calculate the values
  var dayOfYear = DateTime.Now.DayOfYear;
  var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
  var daysRemaining = 365 + leapYearExtra - dayOfYear;

  // Display the message
  if (daysRemaining == 1) {
    TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
  } else {
    TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
  }
}
```

Si usa el método de la interfaz de usuario basada en código, reemplace el comentario `// Insert code to power extension here...` por el nuevo código anterior. Después de llamar a la implementación base (e insertar una etiqueta para la versión basada en código), este código realiza un cálculo sencillo para obtener el día del año y cuántos días quedan. A continuación, muestra el mensaje en la etiqueta (`TodayMessage`) que creó en el diseño de la interfaz de usuario.

Tenga en cuenta que este proceso es similar al proceso normal de escritura de una aplicación. El `UIViewController` de una extensión tiene el mismo ciclo de vida que un controlador de vista en una aplicación, salvo que las extensiones no tienen modos en segundo plano y no se suspenden cuando el usuario ha terminado de usarlas. En su lugar, las extensiones se inicializan repetidamente y se anula la asignación según sea necesario.

### <a name="creating-the-container-app-user-interface"></a>Crear la interfaz de usuario de la aplicación contenedora

En este tutorial, la aplicación contenedora se usa simplemente como método para enviar e instalar la extensión y no proporciona ninguna funcionalidad propia. Edite el archivo de `Main.storyboard` de TodayContainer y agregue texto que defina la función de la extensión y cómo instalarlo:

[![](extensions-images/today10.png "Edit the TodayContainers Main.storyboard file and add some text defining the Extensions function and how to install it")](extensions-images/today10.png#lightbox)

Guarde los cambios en el guion gráfico.

### <a name="testing-the-extension"></a>Probar la extensión

Para probar la extensión en el simulador de iOS, ejecute la aplicación **TodayContainer** . Se mostrará la vista principal del contenedor:

[![](extensions-images/run01.png "The containers main view will be displayed")](extensions-images/run01.png#lightbox)

Después, presione el botón **Inicio** en el simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el **centro de notificaciones**, seleccione la pestaña **hoy** y haga clic en el botón **Editar** :

[![](extensions-images/run02.png "Hit the Home button in the Simulator, swipe down from the top of the screen to open the Notification Center, select the Today tab and click the Edit button")](extensions-images/run02.png#lightbox)

Agregue la extensión **DaysRemaining** a la vista de **hoy** y haga clic en el botón **listo** :

[![](extensions-images/run03.png "Add the DaysRemaining Extension to the Today view and click the Done button")](extensions-images/run03.png#lightbox)

El nuevo widget se agregará a la vista **hoy** y se mostrarán los resultados:

[![](extensions-images/run04.png "The new widget will be added to the Today view and the results will be displayed")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicación con la aplicación host

La extensión de ejemplo de hoy que creó anteriormente no se comunica con su aplicación host (la pantalla **hoy** ). En caso de que lo hiciera, usaría la propiedad [ExtensionContext](xref:Foundation.NSExtensionContext) de las clases `TodayViewController` o `CodeBasedViewController`. 

En el caso de las extensiones que recibirán datos de sus aplicaciones host, los datos tienen el formato de una matriz de objetos [NSExtensionItem](xref:Foundation.NSExtensionItem) almacenados en la propiedad [InputItems](xref:Foundation.NSExtensionContext.InputItems) de [ExtensionContext](xref:Foundation.NSExtensionContext) del `UIViewController` de la extensión.

Otra extensión, como las extensiones de edición de fotografías, puede distinguir entre el usuario que completa o cancela el uso. Esto se devolverá a la aplicación host a través de los métodos [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) y [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) de la propiedad [ExtensionContext](xref:Foundation.NSExtensionContext) .

Para obtener más información, consulte la guía de programación de la [extensión de aplicaciones](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)de Apple.

## <a name="communicating-with-the-parent-app"></a>Comunicación con la aplicación primaria

Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- [Configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [Valores NSUserDefaults compartidos](~/ios/app-fundamentals/user-defaults.md).
- [Archivos compartidos](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obtener más información, consulte la sección [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) de nuestra documentación sobre **Cómo trabajar con capacidades** .

## <a name="mobilecoreservices"></a>MobileCoreServices

Al trabajar con extensiones, use un identificador de tipo uniforme (UTI) para crear y manipular los datos que se intercambian entre la aplicación, otras aplicaciones o servicios.

La clase estática `MobileCoreServices.UTType` define las siguientes propiedades auxiliares relacionadas con las definiciones de `kUTType...` de Apple:

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

Vea el ejemplo siguiente:

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

Para obtener más información, consulte la sección [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) de nuestra documentación sobre **Cómo trabajar con capacidades** .

## <a name="precautions-and-considerations"></a>Precauciones y consideraciones

Las extensiones tienen mucha menos memoria disponible que las aplicaciones. Se espera que funcionen rápidamente y con una mínima intrusión en el usuario y la aplicación en la que se hospedan. Sin embargo, una extensión también debe proporcionar una función distintiva y útil a la aplicación de consumo con una interfaz de usuario con marca que permita al usuario identificar la aplicación de contenedor o desarrollador de la extensión a la que pertenecen.

Teniendo en cuenta estos estrictos requisitos, solo debe implementar las extensiones que se han probado y optimizado exhaustivamente para el rendimiento y el consumo de memoria. 

## <a name="summary"></a>Resumen

En este documento se han tratado extensiones, qué son, el tipo de puntos de extensión y las limitaciones conocidas impuestas por iOS en una extensión. Se ha explicado la creación, distribución, instalación y ejecución de extensiones y el ciclo de vida de la extensión. Ha proporcionado un tutorial para crear un widget de **hoy en día** que muestra dos maneras de crear la interfaz de usuario del widget mediante guiones gráficos o código. Se mostró cómo probar una extensión en el simulador de iOS. Por último, se ha explicado brevemente la comunicación con la aplicación host y algunas precauciones y consideraciones que deben tenerse en cuenta al desarrollar una extensión. 

## <a name="related-links"></a>Vínculos relacionados

- [ContainerApp (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/intro-to-extensions)
- [Crear extensiones en Xamarin. iOS (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
