---
title: Extensiones de iOS en Xamarin.iOS
description: En este documento se describen las extensiones, que son widgets presentados por iOS en el contexto estándar, como en el centro de notificaciones. Describe cómo crear una extensión y comunicarse con ella desde la aplicación primaria.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 05/12/2020
ms.openlocfilehash: d305f671ef68e9a1eded61936f3f32cc8769393f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436299"
---
# <a name="ios-extensions-in-xamarinios"></a>extensiones de iOS en Xamarin. iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Crear extensiones en vídeo de iOS**

Las extensiones, tal y como se introdujeron en iOS 8, están especializadas `UIViewControllers` que se presentan mediante iOS dentro de contextos estándar, como en el **centro de notificaciones**, como tipos de teclado personalizados solicitados por el usuario para realizar entradas especializadas u otros contextos, como editar una fotografía en la que la extensión puede proporcionar filtros de efectos especiales.

Todas las extensiones se instalan junto con una aplicación de contenedor (con ambos elementos escritos mediante las API unificadas de 64 bits) y se activan desde un punto de extensión determinado en una aplicación host. Y dado que se usarán como complementos para las funciones del sistema existentes, deben ser de alto rendimiento, eficaces y robustos.

## <a name="extension-points"></a>Puntos de extensión

|Tipo|Descripción|Punto de extensión|Aplicación host|
|--- |--- |--- |--- |
|Acción|Editor o visor especializado para un tipo de medio determinado|`com.apple.ui-services`|Any|
|Proveedor de documentos|Permite que la aplicación use un almacén de documentos remoto|`com.apple.fileprovider-ui`|Aplicaciones que usan un [UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|Teclado|Teclados alternativos|`com.apple.keyboard-service`|Any|
|Edición de fotografías|Manipulación y edición de fotografías|`com.apple.photo-editing`|Editor de fotos. app|
|Compartir|Comparte datos con redes sociales, servicios de mensajería, etc.|`com.apple.share-services`|Any|
|Hoy|"Widgets" que aparecen en la pantalla de hoy o en el centro de notificaciones|`com.apple.widget-extensions`|Hoy y centro de notificaciones|

Se han agregado puntos de extensión adicionales en [iOS 10](~/ios/platform/introduction-to-ios10/index.md#app-extensions) e [iOS 12](~/ios/platform/introduction-to-ios12/index.md#notification-improvements). Puede encontrar la tabla completa de todos los tipos admitidos en la guía de programación de la [extensión de aplicación de iOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2).

## <a name="limitations"></a>Limitaciones

Las extensiones tienen una serie de limitaciones, algunas de las cuales son universales para todos los tipos (por ejemplo, ningún tipo de extensión puede tener acceso a las cámaras o los micrófonos), mientras que otros tipos de extensión pueden tener limitaciones específicas en su uso (por ejemplo, los teclados personalizados no se pueden usar para los campos de entrada de datos seguros, como las contraseñas).

Las limitaciones universales son:

- El [Kit de mantenimiento](~/ios/platform/healthkit.md) y los marcos de [interfaz de usuario del kit de eventos](~/ios/platform/eventkit.md) no están disponibles
- Las extensiones no pueden usar [modos en segundo plano extendidos](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- Las extensiones no pueden tener acceso a las cámaras o micrófonos del dispositivo (aunque pueden acceder a archivos multimedia existentes)
- Las extensiones no pueden recibir datos de colocación de aire (aunque pueden transmitir datos mediante Air Drop)
- [UIActionSheet](xref:UIKit.UIActionSheet) y [UIAlertView](xref:UIKit.UIAlertView) no están disponibles; las extensiones deben usar [UIAlertController](xref:UIKit.UIAlertController)
- Varios miembros de [UIApplication](xref:UIKit.UIApplication) no están disponibles: [UIApplication. SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication. OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication. BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) y [UIApplication. EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS exige un límite de uso de memoria de 16 MB en las extensiones de hoy.
- De forma predeterminada, las extensiones de teclado no tienen acceso a la red. Esto afecta a la depuración en el dispositivo (la restricción no se aplica en el simulador), ya que Xamarin. iOS requiere acceso a la red para que la depuración funcione. Es posible solicitar el acceso a la red estableciendo el `Requests Open Access` valor de info. plist del proyecto en `Yes` . Consulte la guía de [teclado personalizada](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) de Apple para obtener más información sobre las limitaciones de la extensión del teclado.

Para conocer las limitaciones individuales, consulte la [Guía de programación](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)de la extensión de aplicaciones de Apple.

## <a name="distributing-installing-and-running-extensions"></a>Distribuir, instalar y ejecutar extensiones

Las extensiones se distribuyen desde una aplicación de contenedor, que, a su vez, se envía y se distribuye a través de la tienda de aplicaciones. La extensión (s) distribuida con la aplicación se instala en ese momento, pero el usuario debe habilitar cada extensión explícitamente. Los diferentes tipos de extensiones se habilitan de maneras diferentes; algunos requieren que el usuario navegue hasta la aplicación de **configuración** y los habilite desde allí. Mientras que otras se habilitan en el punto de uso, como la habilitación de una extensión de uso compartido al enviar una foto.

La aplicación en la que se usa la extensión (donde el usuario encuentra el punto de extensión) se conoce como la **aplicación host**, ya que es la aplicación que hospeda la extensión cuando se ejecuta. La aplicación que instala la extensión es la **aplicación contenedora**, ya que es la aplicación que contenía la extensión cuando se instaló.  

Normalmente, la aplicación contenedora describe la extensión y dirige al usuario a través del proceso de habilitación.

## <a name="debug-and-release-versions-of-extensions"></a>Versiones de depuración y lanzamiento de extensiones

Los límites de memoria para ejecutar extensiones de aplicaciones son significativamente menores que los límites de memoria que se aplican a una aplicación en primer plano. Los simuladores que ejecutan iOS tienen menos restricciones aplicadas a las extensiones y puede ejecutar la extensión sin problemas. Sin embargo, si se ejecuta la misma extensión en un dispositivo, pueden producirse resultados inesperados, como el bloqueo de la extensión o la finalización agresiva del sistema. Por lo tanto, asegúrese de compilar y probar la extensión en un dispositivo antes de enviarlo.

Debe asegurarse de que se aplica la siguiente configuración al proyecto de contenedor y a todas las extensiones a las que se hace referencia:

1. Cree un paquete de aplicación en la configuración de **lanzamiento** .
1. En la configuración del proyecto de **compilación de iOS** , establezca la opción de **comportamiento del vinculador** en **vincular solo SDK de marco** o **vincular todo**.
1. En la configuración del proyecto de **depuración de iOS** , desactive la opción **Habilitar depuración** y **Habilitar generación de perfiles** .

## <a name="extension-lifecycle"></a>Ciclo de vida de la extensión

Una extensión puede ser tan simple como una sola [UIViewController](xref:UIKit.UIViewController) o extensiones más complejas que presenten varias pantallas de interfaz de usuario. Cuando el usuario encuentra un punto de _extensión_ (por ejemplo, al compartir una imagen), tendrá la oportunidad de elegir entre las extensiones registradas para ese punto de extensión.

Si eligen una de las extensiones de la aplicación, se `UIViewController` creará una instancia de y comenzará el ciclo de vida del controlador de vista normal. Sin embargo, a diferencia de una aplicación normal, que están suspendidas pero no suelen terminar cuando el usuario termina de interactuar con ellas, las extensiones se cargan, ejecutan y finalizan repetidamente.

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

- Debe tener un identificador de paquete que empiece por el identificador de lote de la aplicación contenedora. Por ejemplo, si la aplicación contenedora tiene un identificador de paquete `com.myCompany.ContainerApp` , el identificador de la extensión podría ser `com.myCompany.ContainerApp.MyExtension` :

  ![Identificadores de agrupación](extensions-images/bundleidentifiers.png)
- Debe definir la clave `NSExtensionPointIdentifier` , con un valor adecuado (por ejemplo, `com.apple.widget-extension` para un widget del centro de notificaciones de **hoy en día** ), en su `Info.plist` archivo.
- También *debe definir la* `NSExtensionMainStoryboard` clave o la `NSExtensionPrincipalClass` clave en su `Info.plist` archivo con un valor adecuado:
  - Use la `NSExtensionMainStoryboard` clave para especificar el nombre del guion gráfico que presenta la interfaz de usuario principal para la extensión (menos `.storyboard` ). Por ejemplo, `Main` para el `Main.storyboard` archivo.
  - Use la `NSExtensionPrincipalClass` clave para especificar la clase que se inicializará cuando se inicie la extensión. El valor debe coincidir con el valor de **registro** de su `UIViewController` :

  ![Registro de la clase principal](extensions-images/registerandprincipalclass.png)

Los tipos específicos de extensiones pueden tener requisitos adicionales. Por ejemplo, **hoy** o la clase principal de la extensión del **centro de notificaciones** debe implementar [INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding).

> [!IMPORTANT]
> Si inicia el proyecto mediante una de las plantillas de extensiones proporcionadas por Visual Studio para Mac, la mayoría de los requisitos (si no todos) se proporcionarán y se cumplirán automáticamente con la plantilla.

## <a name="walkthrough"></a>Tutorial

En el siguiente tutorial, creará un widget de ejemplo **hoy** en el que se calcula el día y el número de días restantes del año:

[![Un widget de ejemplo hoy que calcula el día y el número de días restantes del año](extensions-images/carpediemscreenshot-sm.png)](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Crear la solución

Para crear la solución necesaria, haga lo siguiente:

1. En primer lugar, cree un nuevo proyecto de **aplicación de vista única** de iOS y haga clic en el botón **siguiente** :

    [![En primer lugar, cree un nuevo proyecto de aplicación de vista única de iOS y haga clic en el botón siguiente.](extensions-images/today01.png)](extensions-images/today01.png#lightbox)
2. Llame al proyecto `TodayContainer` y haga clic en el botón **siguiente** :

    [![Llame al proyecto TodayContainer y haga clic en el botón siguiente.](extensions-images/today02.png)](extensions-images/today02.png#lightbox)
3. Compruebe el **nombre del proyecto** y el **nombredesolución** y haga clic en el botón **crear** para crear la solución:

    [![Compruebe el nombre del proyecto y el SolutionName y haga clic en el botón crear para crear la solución.](extensions-images/today03.png)](extensions-images/today03.png#lightbox)
4. Después, en el **Explorador de soluciones**, haga clic con el botón derecho en la solución y agregue un nuevo proyecto de **extensión de iOS** a partir de la plantilla de **extensión de hoy** :

    [![Después, en el Explorador de soluciones, haga clic con el botón derecho en la solución y agregue un nuevo proyecto de extensión de iOS a partir de la plantilla de extensión de hoy.](extensions-images/today04.png)](extensions-images/today04.png#lightbox)
5. Llame al proyecto `DaysRemaining` y haga clic en el botón **siguiente** :

    [![Llame al proyecto DaysRemaining y haga clic en el botón siguiente.](extensions-images/today05.png)](extensions-images/today05.png#lightbox)
6. Revise el proyecto y haga clic en el botón **crear** para crearlo:

    [![Revise el proyecto y haga clic en el botón crear para crearlo.](extensions-images/today06.png)](extensions-images/today06.png#lightbox)

La solución resultante debería tener ahora dos proyectos, como se muestra aquí:

[![La solución resultante debería tener ahora dos proyectos, como se muestra aquí.](extensions-images/today07.png)](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Crear la interfaz de usuario de la extensión

A continuación, tendrá que diseñar la interfaz para el widget **hoy** . Esto puede hacerse mediante un guión gráfico o mediante la creación de la interfaz de usuario en el código. Ambos métodos se tratarán con más detalle.

#### <a name="using-storyboards"></a>Usar guiones gráficos

Para compilar la interfaz de usuario con un guion gráfico, haga lo siguiente:

1. En el **Explorador de soluciones**, haga doble clic en el archivo del proyecto de extensión `Main.storyboard` para abrirlo para su edición:

    [![Haga doble clic en el archivo main. Storyboard del proyecto de extensión para abrirlo y editarlo.](extensions-images/today08.png)](extensions-images/today08.png#lightbox)
2. Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario por plantilla y asígnele el **nombre** `TodayMessage` en la pestaña **Widget** del **Explorador de propiedades**:

    [![Seleccione la etiqueta que se agregó automáticamente a la interfaz de usuario por plantilla y asígnele el nombre TodayMessage en la pestaña widget del explorador de propiedades.](extensions-images/today09.png)](extensions-images/today09.png#lightbox)
3. Guarde los cambios en el guion gráfico.

#### <a name="using-code"></a>Usar código

Para compilar la interfaz de usuario en el código, haga lo siguiente:

1. En el **Explorador de soluciones**, seleccione el proyecto **DaysRemaining** , agregue una nueva clase y llámela `CodeBasedViewController` :

    [![Aelect el proyecto DaysRemaining, agregue una nueva clase y llámela CodeBasedViewController](extensions-images/code01.png)](extensions-images/code01.png#lightbox)
2. De nuevo, en el **Explorador de soluciones**, haga doble clic en `Info.plist` archivo de extensión para abrirlo para su edición:

    [![Haga doble clic en el archivo info. plist de extensiones para abrirlo y editarlo.](extensions-images/code02.png)](extensions-images/code02.png#lightbox)
3. Seleccione la **vista Código fuente** (en la parte inferior de la pantalla) y abra el `NSExtension` nodo:

    [![Seleccione la vista Código fuente en la parte inferior de la pantalla y abra el nodo NSExtension](extensions-images/code03.png)](extensions-images/code03.png#lightbox)
4. Quite la `NSExtensionMainStoryboard` clave y agregue una `NSExtensionPrincipalClass` con el valor `CodeBasedViewController` :

    [![Quite la clave NSExtensionMainStoryboard y agregue NSExtensionPrincipalClass con el valor CodeBasedViewController](extensions-images/code04.png)](extensions-images/code04.png#lightbox)
5. Guarde los cambios.

Después, edite el `CodeBasedViewController.cs` archivo y haga que tenga un aspecto similar al siguiente:

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

Tenga en cuenta que `[Register("CodeBasedViewController")]` coincide con el valor especificado para el `NSExtensionPrincipalClass` anterior.

### <a name="coding-the-extension"></a>Codificación de la extensión

Con la interfaz de usuario creada, abra el `TodayViewController.cs` archivo o `CodeBasedViewController.cs` (basado en el método utilizado para crear la interfaz de usuario anterior), cambie el método **ViewDidLoad** y haga que tenga un aspecto similar al siguiente:

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

Si usa el método de la interfaz de usuario basada en código, reemplace el `// Insert code to power extension here...` Comentario con el nuevo código anterior. Después de llamar a la implementación base (e insertar una etiqueta para la versión basada en código), este código realiza un cálculo sencillo para obtener el día del año y cuántos días quedan. A continuación, muestra el mensaje en la etiqueta ( `TodayMessage` ) que creó en el diseño de la interfaz de usuario.

Tenga en cuenta que este proceso es similar al proceso normal de escritura de una aplicación. Una extensión `UIViewController` tiene el mismo ciclo de vida que un controlador de vista en una aplicación, salvo que las extensiones no tienen modos en segundo plano y no se suspenden cuando el usuario ha terminado de usarlas. En su lugar, las extensiones se inicializan repetidamente y se anula la asignación según sea necesario.

### <a name="creating-the-container-app-user-interface"></a>Crear la interfaz de usuario de la aplicación contenedora

En este tutorial, la aplicación contenedora se usa simplemente como método para enviar e instalar la extensión y no proporciona ninguna funcionalidad propia. Edite el archivo de TodayContainer `Main.storyboard` y agregue texto que defina la función de la extensión y cómo instalarlo:

[![Edite el archivo main. Storyboard TodayContainers y agregue texto que defina la función Extensions y aprenda a instalarla.](extensions-images/today10.png)](extensions-images/today10.png#lightbox)

Guarde los cambios en el guion gráfico.

### <a name="testing-the-extension"></a>Probar la extensión

Para probar la extensión en el simulador de iOS, ejecute la aplicación **TodayContainer** . Se mostrará la vista principal del contenedor:

[![Se mostrará la vista principal de contenedores.](extensions-images/run01.png)](extensions-images/run01.png#lightbox)

Después, presione el botón **Inicio** en el simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el **centro de notificaciones**, seleccione la pestaña **hoy** y haga clic en el botón **Editar** :

[![Presione el botón Inicio del simulador, deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el centro de notificaciones, seleccione la pestaña hoy y haga clic en el botón Editar.](extensions-images/run02.png)](extensions-images/run02.png#lightbox)

Agregue la extensión **DaysRemaining** a la vista de **hoy** y haga clic en el botón **listo** :

[![Agregue la extensión DaysRemaining a la vista hoy y haga clic en el botón listo.](extensions-images/run03.png)](extensions-images/run03.png#lightbox)

El nuevo widget se agregará a la vista **hoy** y se mostrarán los resultados:

[![El nuevo widget se agregará a la vista hoy y se mostrarán los resultados.](extensions-images/run04.png)](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Comunicación con la aplicación host

La extensión de ejemplo de hoy que creó anteriormente no se comunica con su aplicación host (la pantalla **hoy** ). En caso de que lo hiciera, usaría la propiedad [ExtensionContext](xref:Foundation.NSExtensionContext) de `TodayViewController` las `CodeBasedViewController` clases o.

En el caso de las extensiones que recibirán datos de sus aplicaciones host, los datos tienen el formato de una matriz de objetos [NSExtensionItem](xref:Foundation.NSExtensionItem) almacenados en la propiedad [InputItems](xref:Foundation.NSExtensionContext.InputItems) de [ExtensionContext](xref:Foundation.NSExtensionContext) de la de la extensión `UIViewController` .

Otras extensiones, como las extensiones de edición de fotografías, pueden distinguir entre el usuario que completa o cancela el uso. Esto se devolverá a la aplicación host a través de los métodos [CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*) y [CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*) de la propiedad [ExtensionContext](xref:Foundation.NSExtensionContext) .

Para obtener más información, consulte la guía de programación de la [extensión de aplicaciones](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)de Apple.

## <a name="communicating-with-the-parent-app"></a>Comunicación con la aplicación primaria

Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- [Apple Watch configuración](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults compartido](~/ios/app-fundamentals/user-defaults.md).
- [Archivos compartidos](~/ios/watchos/app-fundamentals/parent-app.md#files).

Para obtener más información, consulte la sección [grupos de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) de nuestra documentación sobre **Cómo trabajar con capacidades** .

## <a name="mobilecoreservices"></a>MobileCoreServices

Al trabajar con extensiones, use un identificador de tipo uniforme (UTI) para crear y manipular los datos que se intercambian entre la aplicación, otras aplicaciones o servicios.

La `MobileCoreServices.UTType` clase estática define las siguientes propiedades auxiliares relacionadas con las definiciones de Apple `kUTType...` :

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

Observe el ejemplo siguiente:

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

- [ContainerApp (ejemplo)](/samples/xamarin/ios-samples/intro-to-extensions)
- [Crear extensiones en Xamarin. iOS (vídeo)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
- [Optimizar la eficacia y el rendimiento de una extensión de aplicación de iOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)