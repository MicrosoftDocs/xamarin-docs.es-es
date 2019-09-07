---
title: Entrega en Xamarin. iOS
description: En este artículo se explica cómo trabajar con la entrega en una aplicación de Xamarin. iOS para transferir actividades de usuario entre aplicaciones que se ejecutan en otros dispositivos del usuario.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 52ee92eca5fa0b3108b2ef96ef81bfb939e61a6c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752918"
---
# <a name="handoff-in-xamarinios"></a>Entrega en Xamarin. iOS

_En este artículo se explica cómo trabajar con la entrega en una aplicación de Xamarin. iOS para transferir actividades de usuario entre aplicaciones que se ejecutan en otros dispositivos del usuario._

Apple presentó la entrega en iOS 8 y OS X Yosemite (10,10) para proporcionar un mecanismo común para que el usuario transfiera las actividades iniciadas en uno de sus dispositivos, a otro dispositivo que ejecute la misma aplicación u otra aplicación que admita la misma actividad.

[![](handoff-images/handoff02.png "Ejemplo de cómo realizar una operación de entrega")](handoff-images/handoff02.png#lightbox)

En este artículo se ofrece una visión rápida de cómo habilitar el uso compartido de actividades en una aplicación de Xamarin. iOS y se trata el marco de trabajo de entrega en detalle:

## <a name="about-handoff"></a>Acerca de la entrega

Apple incorporó la entrega (también conocida como continuidad) en iOS 8 y OS X Yosemite (10,10) como una manera para que el usuario inicie una actividad en uno de sus dispositivos (iOS o Mac) y continúe esa misma actividad en otro de sus dispositivos (tal y como se identifica en el iClou del usuario). Cuenta d).

La entrega se amplió en iOS 9 para admitir también nuevas funcionalidades de búsqueda mejoradas. Para obtener más información, consulte nuestra documentación sobre [mejoras de búsqueda](~/ios/platform/search/index.md) .

Por ejemplo, el usuario puede iniciar un correo electrónico en su iPhone y continuar sin problemas con el correo electrónico en su equipo Mac, con la misma información de mensaje rellenada y el cursor en la misma ubicación que la dejó en iOS.

Cualquiera de las aplicaciones que comparten el mismo _identificador de equipo_ pueden usar la entrega para continuar con las actividades de usuario en todas las aplicaciones, siempre y cuando estas aplicaciones se entreguen a través de iTunes App Store o estén firmadas por un desarrollador registrado (para aplicaciones Mac, Enterprise o ad hoc).

Las `NSDocument` aplicaciones `UIDocument` de o basadas automáticamente tienen compatibilidad con la entrega integrada y requieren cambios mínimos para admitir la entrega.

### <a name="continuing-user-activities"></a>Continuar actividades de usuario

La `NSUserActivity` clase (junto con algunos cambios pequeños en `UIKit` y `AppKit`) proporciona compatibilidad para definir la actividad de un usuario que podría continuar en otro de los dispositivos del usuario.

Para que una actividad se pase a otro de los dispositivos del usuario, debe encapsularse en una instancia `NSUserActivity`, marcada como _actividad actual_, tener su conjunto de carga (los datos usados para realizar la continuación) y la actividad debe ser se transmiten a ese dispositivo.

La entrega pasa el mínimo de información para definir la actividad que se va a continuar, con paquetes de datos más grandes que se sincronizan a través de iCloud.

En el dispositivo receptor, el usuario recibirá una notificación de que hay una actividad disponible para su continuación. Si el usuario decide continuar la actividad en el nuevo dispositivo, se inicia la aplicación especificada (si aún no se está ejecutando) y la carga de `NSUserActivity` se usa para reiniciar la actividad.

[![](handoff-images/handoffinteractions.png "Información general sobre la continuación de actividades de usuario")](handoff-images/handoffinteractions.png#lightbox)

Solo las aplicaciones que comparten el mismo identificador de equipo de desarrollador y responden a un _tipo de actividad_ determinado son válidas para su continuación. Una aplicación define los tipos de actividad que admite con la `NSUserActivityTypes` clave de su archivo **info. plist** . Dado esto, un dispositivo continuo elige la aplicación para realizar la continuación en función del identificador de equipo, el tipo de actividad y, opcionalmente, el título de la _actividad_.

La aplicación receptora usa información del `NSUserActivity` `UserInfo` Diccionario de para configurar su interfaz de usuario y restaurar el estado de la actividad determinada para que la transición parezca perfecta para el usuario final.

Si la continuación requiere más información de la que se puede enviar de `NSUserActivity`forma eficaz a través de, la aplicación de reanudación puede enviar una llamada a la aplicación de origen y establecer una o más secuencias para transmitir los datos necesarios. Por ejemplo, si la actividad estaba editando un documento de texto grande con varias imágenes, sería necesario el streaming para transferir la información necesaria para continuar con la actividad en el dispositivo receptor. Para obtener más información, consulte la sección [compatibilidad con secuencias de continuación](#supporting-continuation-streams) .

Como se indicó anteriormente `NSDocument` , `UIDocument` o las aplicaciones basadas en admiten automáticamente la compatibilidad con la entrega. Para obtener más información, consulte la sección [compatibilidad con la entrega en aplicaciones basadas en documentos](#supporting-handoff-in-document-based-apps) más adelante.

### <a name="the-nsuseractivity-class"></a>La clase NSUserActivity

La `NSUserActivity` clase es el objeto principal de un intercambio de entrega y se utiliza para encapsular el estado de una actividad de usuario que está disponible para la continuación. Una aplicación creará una instancia de una `NSUserActivity` copia de para cualquier actividad que admita y desea continuar en otro dispositivo. Por ejemplo, el editor de documentos crearía una actividad para cada documento abierto actualmente. Sin embargo, solo el documento más hacia la frente (mostrado en la ventana o pestaña frontal) es la _actividad actual_ y está disponible para su continuación.

Una instancia de `NSUserActivity` se identifica mediante sus `ActivityType` propiedades y `Title` . La `UserInfo` propiedad Dictionary se usa para llevar información sobre el estado de la actividad. Establezca la `NeedsSave` propiedad en `true` si desea cargar de forma diferida la información de estado `NSUserActivity`mediante el delegado de. Use el `AddUserInfoEntries` método para combinar datos nuevos de otros clientes en el `UserInfo` Diccionario según sea necesario para conservar el estado de la actividad.

### <a name="the-nsuseractivitydelegate-class"></a>La clase NSUserActivityDelegate

Se utiliza para mantener actualizada la información `NSUserActivity` `UserInfo` del Diccionario de y sincronizarla con el estado actual de la actividad. `NSUserActivityDelegate` Cuando el sistema necesita que la información de la actividad se actualice (por ejemplo, antes de la continuación en otro dispositivo), `UserActivityWillSave` llama al método del delegado.

Deberá implementar el `UserActivityWillSave` método y realizar cualquier cambio `NSUserActivity` en (por ejemplo `UserInfo`, `Title`,, etc.) para asegurarse de que todavía refleja el estado de la actividad actual. Cuando el sistema llame al `UserActivityWillSave` método, se `NeedsSave` borrará la marca. Si modifica cualquiera de las propiedades de datos de la actividad, deberá volver a establecer `NeedsSave` en. `true`

En lugar de usar el `UserActivityWillSave` método presentado anteriormente, también puede tener `UIKit` o `AppKit` administrar la actividad de usuario automáticamente. Para ello, establezca la propiedad del objeto de `UserActivity` respuesta e implemente el `UpdateUserActivityState` método. Para obtener más información, consulte la sección [compatibilidad con la entrega en los respondedores](#supporting-handoff-in-responders) .

### <a name="app-framework-support"></a>Compatibilidad con el marco de trabajo de aplicaciones

Tanto `UIKit` (iOS) como `AppKit` (OS X) proporcionan compatibilidad integrada para la entrega en las `NSDocument`clases, respondedor (`UIResponder`/`NSResponder`) y `AppDelegate` . Aunque cada sistema operativo implementa la entrega de forma ligeramente diferente, el mecanismo básico y las API son los mismos.

#### <a name="user-activities-in-document-based-apps"></a>Actividades de usuario en aplicaciones basadas en documentos

Las aplicaciones de iOS y OS X basadas en documentos incorporan automáticamente compatibilidad con la entrega. Para activar esta compatibilidad, deberá agregar una clave y un `NSUbiquitousDocumentUserActivityType` valor para cada `CFBundleDocumentTypes` entrada en el archivo **info. plist** de la aplicación.

Si esta clave está presente, `NSDocument` y `UIDocument` crean `NSUserActivity` automáticamente instancias para documentos basados en iCloud del tipo especificado. Tendrá que proporcionar un tipo de actividad para cada tipo de documento admitido por la aplicación y varios tipos de documento pueden usar el mismo tipo de actividad. `FileURL` `NSUserActivity` Y rellenan automáticamente `UserInfo` la propiedad de con el valor de su propiedad. `UIDocument` `NSDocument`

En OS X, el `NSUserActivity` administrado por `AppKit` y los que están asociados a los respondedores se convierten automáticamente en la actividad actual cuando la ventana del documento se convierte en la ventana principal. En iOS, para `NSUserActivity` los objetos administrados por `UIKit`, debe llamar `BecomeCurrent` al método explícitamente o `UIViewController` tener la `UserActivity` propiedad del documento establecida en cuando la aplicación llegue al primer plano.

`AppKit`restaurará automáticamente cualquier `UserActivity` propiedad creada de este modo en OS X. Esto sucede si el `ContinueUserActivity` método devuelve `false` o si no está implementado. En esta situación, el documento se abre con el `OpenDocument` método `NSDocumentController` de y, a continuación, recibirá `RestoreUserActivityState` una llamada al método.

Para obtener más información, consulte la sección [compatibilidad con las aplicaciones basadas en documentos](#supporting-handoff-in-document-based-apps) .

#### <a name="user-activities-and-responders"></a>Actividades de usuario y respondedores

Y pueden administrar automáticamente una actividad de usuario si se establece como la propiedad de un objeto de `UserActivity` respuesta. `AppKit` `UIKit` Si el estado se ha modificado, deberá establecer la `NeedsSave` propiedad del del `UserActivity` respondedor en `true`. El sistema guardará `UserActivity` automáticamente cuando sea necesario, después de dar tiempo al respondedor para actualizar el estado mediante una `UpdateUserActivityState` llamada a su método.

Si varios respondedores comparten una sola `NSUserActivity` instancia, reciben una `UpdateUserActivityState` devolución de llamada cuando el sistema actualiza el objeto de actividad del usuario. El respondedor debe llamar `AddUserInfoEntries` al método para actualizar el `NSUserActivity`Diccionario de `UserInfo` para que refleje el estado actual de la actividad en este momento. El `UserInfo` diccionario se borra antes de `UpdateUserActivityState` cada llamada.

Para desasociarse de una actividad, un respondedor puede establecer `UserActivity` su propiedad `null`en. Cuando una instancia administrada `NSUserActivity` del marco de trabajo de la aplicación no tiene ningún documento o respondedor asociado, se invalida automáticamente.

Para obtener más información, consulte la sección [compatibilidad con la entrega en los respondedores](#supporting-handoff-in-responders) .

#### <a name="user-activities-and-the-appdelegate"></a>Actividades de usuario y AppDelegate

La aplicación es `AppDelegate` su punto de entrada principal al administrar una continuación de la entrega. Cuando el usuario responde a una notificación de entrega, se inicia la aplicación adecuada (si aún no se está ejecutando) `WillContinueUserActivityWithType` y se llama `AppDelegate` al método de. En este momento, la aplicación debe informar al usuario de que se está iniciando la continuación.

La `NSUserActivity` instancia se entrega cuando se `AppDelegate`llama `ContinueUserActivity` al método de. En este punto, debe configurar la interfaz de usuario de la aplicación y continuar con la actividad especificada.

Vea la sección implementación de la [entrega](#implementing-handoff) a continuación para obtener más información.

## <a name="enabling-handoff-in-a-xamarin-app"></a>Habilitar la entrega en una aplicación de Xamarin

Debido a los requisitos de seguridad impuestos por la entrega, una aplicación de Xamarin. iOS que usa el marco de entrega debe estar configurada correctamente en el portal para desarrolladores de Apple y en el archivo de proyecto de Xamarin. iOS.

Haga lo siguiente:

1. Inicie sesión en el [portal para desarrolladores de Apple](https://developer.apple.com).
2. Haga clic en **certificados, identificadores & perfiles**.
3. Si todavía no lo ha hecho, haga clic en **identificadores** y cree un identificador para la aplicación (por `com.company.appname`ejemplo,). en caso contrario, edite el identificador existente.
4. Asegúrese de que se ha comprobado el identificador especificado en el servicio **iCloud** :

    [![](handoff-images/provision01.png "Habilitar el servicio iCloud para el identificador especificado")](handoff-images/provision01.png#lightbox)
5. Guarde los cambios.
6. Haga clic en **aprovisionar perfiles** > **desarrollo** y cree un nuevo perfil de aprovisionamiento de desarrollo para la aplicación:

    [![](handoff-images/provision02.png "Crear un nuevo perfil de aprovisionamiento de desarrollo para la aplicación")](handoff-images/provision02.png#lightbox)
7. Descargue e instale el nuevo perfil de aprovisionamiento o use Xcode para descargar e instalar el perfil.
8. Edite las opciones del proyecto de Xamarin. iOS y asegúrese de que está usando el perfil de aprovisionamiento que acaba de crear:

    [![](handoff-images/provision03.png "Seleccione el perfil de aprovisionamiento que acaba de crear.")](handoff-images/provision03.png#lightbox)
9. Después, edite el archivo **info. plist** y asegúrese de que está usando el identificador de aplicación que se usó para crear el perfil de aprovisionamiento:

    [![](handoff-images/provision04.png "Establecimiento del identificador de la aplicación")](handoff-images/provision04.png#lightbox)
10. Desplácese a la sección **modos en segundo plano** y compruebe los elementos siguientes:

    [![](handoff-images/provision05.png "Habilitar los modos en segundo plano requeridos")](handoff-images/provision05.png#lightbox)
11. Guarde los cambios en todos los archivos.

Con esta configuración en contexto, la aplicación ya está lista para tener acceso a las API de la plataforma de entrega. Para obtener información detallada sobre el aprovisionamiento, consulte las guías [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) y aprovisionamiento [de aplicaciones](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="implementing-handoff"></a>Implementación de la entrega

Las actividades de usuario pueden continuar entre las aplicaciones que estén firmadas con el mismo identificador de equipo de desarrollador y que admitan el mismo tipo de actividad. La implementación de la entrega en una aplicación de Xamarin. iOS requiere la creación de un objeto de `UIKit` actividad `AppKit`de usuario (en o), actualizar el estado del objeto para realizar el seguimiento de la actividad y continuar con la actividad en un dispositivo receptor.

### <a name="identifying-user-activities"></a>Identificación de las actividades del usuario

El primer paso para implementar la entrega es identificar los tipos de actividades de usuario que admite la aplicación y ver cuáles de esas actividades son buenos candidatos para la continuación en otro dispositivo. Por ejemplo: una aplicación de ToDo podría admitir la edición de elementos como un _tipo de actividad de usuario_y permitir explorar la lista de elementos disponibles como otra.

Una aplicación puede crear tantos tipos de actividad de usuario como sean necesarios, uno para cualquier función que proporcione la aplicación. Para cada tipo de actividad de usuario, la aplicación necesitará realizar un seguimiento cuando se inicie y finalice una actividad del tipo, y tendrá que mantener la información de estado actualizada para continuar con la tarea en otro dispositivo.

Las actividades de usuario pueden continuar en cualquier aplicación firmada con el mismo identificador de equipo sin ninguna asignación uno a uno entre las aplicaciones emisoras y receptoras. Por ejemplo, una aplicación determinada puede crear cuatro tipos diferentes de actividades, que se usan en aplicaciones individuales diferentes en otro dispositivo. Se trata de una repetición común entre una versión Mac de la aplicación (que puede tener muchas características y funciones) y aplicaciones iOS, donde cada aplicación es más pequeña y se centra en una tarea específica.

### <a name="creating-activity-type-identifiers"></a>Crear identificadores de tipo de actividad

El _identificador de tipo de actividad_ es una cadena corta agregada `NSUserActivityTypes` a la matriz del archivo **info. plist** de la aplicación que se usa para identificar de forma única un tipo de actividad de usuario determinado. Habrá una entrada en la matriz para cada actividad que admita la aplicación. Apple sugiere el uso de una notación de estilo DNS inversa para el identificador de tipo de actividad con el fin de evitar colisiones. Por ejemplo: `com.company-name.appname.activity` para actividades basadas en aplicaciones específicas `com.company-name.activity` o para actividades que se pueden ejecutar en varias aplicaciones.

El identificador de tipo de actividad se usa al crear `NSUserActivity` una instancia de para identificar el tipo de actividad. Cuando una actividad continúa en otro dispositivo, el tipo de actividad (junto con el identificador de equipo de la aplicación) determina la aplicación que se va a iniciar para continuar con la actividad.

Por ejemplo, vamos a crear una aplicación de ejemplo llamada **MonkeyBrowser** ([descarga aquí](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)). Esta aplicación presenta cuatro pestañas, cada una con una dirección URL diferente abierta en una vista de explorador Web. El usuario podrá continuar con cualquier pestaña en un dispositivo iOS diferente que ejecute la aplicación.

Para crear los identificadores de tipo de actividad necesarios para admitir este comportamiento, edite el archivo **info. plist** y cambie a la vista de **código fuente** . Agregue una `NSUserActivityTypes` clave y cree los siguientes identificadores:

[![](handoff-images/type01.png "La clave NSUserActivityTypes y los identificadores necesarios en el editor plist")](handoff-images/type01.png#lightbox)

Hemos creado cuatro nuevos identificadores de tipo de actividad, uno para cada una de las pestañas de la aplicación de ejemplo **MonkeyBrowser** . Al crear sus propias aplicaciones, reemplace el contenido de la `NSUserActivityTypes` matriz por los identificadores de tipo de actividad específicos de las actividades que admite la aplicación.

### <a name="tracking-user-activity-changes"></a>Seguimiento de cambios de actividad del usuario

Cuando se crea una nueva instancia de la `NSUserActivity` clase, se especifica una `NSUserActivityDelegate` instancia para realizar el seguimiento de los cambios en el estado de la actividad. Por ejemplo, se puede usar el código siguiente para realizar el seguimiento de los cambios de estado:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

Se llama al método cuando una secuencia de continuación ha recibido datos de un dispositivo de envío. `UserActivityReceivedData` Para obtener más información, consulte la sección [compatibilidad con secuencias de continuación](#supporting-continuation-streams) .

Se llama al método cuando otro dispositivo ha tomado una actividad del dispositivo actual. `UserActivityWasContinued` Según el tipo de actividad, como agregar un nuevo elemento a una lista de tareas pendientes, la aplicación podría necesitar anular la actividad en el dispositivo de envío.

Se `UserActivityWillSave` llama al método antes de que los cambios en la actividad se guarden y sincronicen en los dispositivos disponibles localmente. Puede utilizar este método para realizar cualquier último cambio en la `UserInfo` propiedad de la `NSUserActivity` instancia antes de enviarla.

### <a name="creating-a-nsuseractivity-instance"></a>Creación de una instancia de NSUserActivity

Cada actividad que su aplicación quiere proporcionar la posibilidad de continuar en otro dispositivo debe encapsularse en una `NSUserActivity` instancia de. La aplicación puede crear tantas actividades como sea necesario y la naturaleza de esas actividades depende de la funcionalidad y las características de la aplicación en cuestión. Por ejemplo, una aplicación de correo electrónico puede crear una actividad para crear un nuevo mensaje y otra para leer un mensaje.

En nuestra aplicación de ejemplo, se `NSUserActivity` crea un nuevo cada vez que el usuario escribe una nueva dirección URL en una de las vistas del explorador Web con pestañas. En el código siguiente se almacena el estado de una pestaña determinada:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Crea un nuevo `NSUserActivity` con uno de los tipos de actividad de usuario creados anteriormente y proporciona un título legible para la actividad. Se adjunta a una instancia de `NSUserActivityDelegate` creada anteriormente para inspeccionar los cambios de estado e informa a iOS de que esta actividad de usuario es la actividad actual.

### <a name="populating-the-userinfo-dictionary"></a>Rellenar el Diccionario UserInfo

Como hemos mencionado anteriormente, la `UserInfo` propiedad de la clase es una `NSDictionary` de los `NSUserActivity` pares clave-valor que se usan para definir el estado de una actividad determinada. Los valores almacenados en `UserInfo` deben ser de uno de los tipos siguientes `NSArray`: `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, o `NSURL`. `NSURL`los valores de datos que señalan a los documentos de iCloud se ajustarán automáticamente para que señalen a los mismos documentos en un dispositivo receptor.

En el ejemplo anterior, creamos un `NSMutableDictionary` objeto y lo rellenamos con una única clave que proporciona la dirección URL que el usuario estaba viendo en la pestaña especificada. El `AddUserInfoEntries` método de la actividad del usuario se usó para actualizar la actividad con los datos que se utilizarán para restaurar la actividad en el dispositivo receptor:

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple recomienda mantener la información que se envía al mínimo para asegurarse de que la actividad se envía de manera oportuna al dispositivo receptor. Si se necesita más información, como se debe editar una imagen adjunta a un documento, se deben usar secuencias de continuación. Vea la sección [compatibilidad con secuencias de continuación](#supporting-continuation-streams) para obtener más detalles.

### <a name="continuing-an-activity"></a>Continuar una actividad

La entrega informará automáticamente a los dispositivos locales de iOS y OS X que están en proximidad física con el dispositivo de origen y que han iniciado sesión en la misma cuenta de iCloud, de la disponibilidad de las actividades de usuario continuadas. Si el usuario decide continuar una actividad en un dispositivo nuevo, el sistema iniciará la aplicación adecuada (según el tipo de actividad y el identificador de equipo) y se debe `AppDelegate` producir información sobre su continuación.

En primer lugar `WillContinueUserActivityWithType` , se llama al método para que la aplicación pueda informar al usuario de que la continuación está a punto de comenzar. Usamos el siguiente código en el archivo **AppDelegate.CS** de la aplicación de ejemplo para administrar una continuación a partir de:

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

En el ejemplo anterior, cada controlador de vista se registra con `AppDelegate` y tiene un método `PreparingToHandoff` público que muestra un indicador de actividad y un mensaje que permite al usuario saber que la actividad está a punto de entregarse al dispositivo actual. Ejemplo:

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity` Se llamaráalmétodoparacontinuarrealmente`AppDelegate` la actividad determinada. Una vez más, desde nuestra aplicación de ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

El método `PerformHandoff` público de cada controlador de vista realiza realmente la entrega y restaura la actividad en el dispositivo actual. En el caso del ejemplo, muestra la misma dirección URL en una pestaña determinada que el usuario estaba explorando en un dispositivo diferente. Ejemplo:

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

El `ContinueUserActivity` método incluye un `UIApplicationRestorationHandler` que se puede llamar para la reanudación de la actividad basada en documentos o en el respondedor. Deberá pasar un `NSArray` objeto o objetos restaurables al controlador de restauración cuando se le llame. Por ejemplo:

```csharp
completionHandler (new NSObject[]{Tab4});
```

Para cada objeto pasado, se `RestoreUserActivityState` llamará a su método. Cada objeto puede usar los datos `UserInfo` del diccionario para restaurar su propio estado. Por ejemplo:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

En el caso de las aplicaciones basadas en documentos, si no `ContinueUserActivity` implementa el método o `false`devuelve `UIKit` , `AppKit` o puede reanudar automáticamente la actividad. Para obtener más información, consulte la sección [compatibilidad con las aplicaciones basadas en documentos](#supporting-handoff-in-document-based-apps) .

### <a name="failing-handoff-gracefully"></a>Error de entrega correcta

Dado que la entrega se basa en la transmisión de información entre una colección de dispositivos iOS y OS X conectados de forma flexible, en ocasiones se produce un error en el proceso de transferencia. Debe diseñar la aplicación para controlar estos errores correctamente e informar al usuario de las situaciones que surjan.

En caso de que se produzca un error `DidFailToContinueUserActivitiy` , se llamará al método `AppDelegate` de. Por ejemplo:

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Debe utilizar el proporcionado `NSError` para proporcionar información al usuario sobre el error.

## <a name="native-app-to-web-browser-handoff"></a>Entrega de aplicación nativa a explorador Web

Es posible que un usuario desee continuar con una actividad sin tener una aplicación nativa adecuada instalada en el dispositivo deseado. En algunas situaciones, una interfaz basada en web puede proporcionar la funcionalidad necesaria y la actividad todavía puede continuar. Por ejemplo, la cuenta de correo electrónico del usuario puede proporcionar una interfaz de usuario basada en web para redactar y leer mensajes.

Si la aplicación nativa de origen conoce la dirección URL de la interfaz web (y la sintaxis necesaria para identificar el elemento determinado que se está continuando), puede codificar esta información `WebpageURL` en la propiedad `NSUserActivity` de la instancia. Si el dispositivo receptor no tiene instalada una aplicación nativa adecuada para controlar la continuación, se puede llamar a la interfaz Web proporcionada.

## <a name="web-browser-to-native-app-handoff"></a>Entrega del explorador Web a la aplicación nativa

Si el usuario estaba usando una interfaz basada en Web en el dispositivo de origen y una aplicación nativa en el dispositivo receptor notifica la parte de dominio de la `WebpageURL` propiedad, el sistema usará esa aplicación para controlar la continuación. El nuevo dispositivo recibirá una `NSUserActivity` instancia que marca el tipo de actividad `BrowsingWeb` como y `WebpageURL` contendrá la dirección URL que el usuario ha visitado `UserInfo` , el Diccionario estará vacío.

Para que una aplicación participe en este tipo de entrega, debe reclamar el dominio en `com.apple.developer.associated-domains` un derecho con el `<service>:<fully qualified domain name>` formato (por ejemplo `activity continuation:company.com`:).

Si el dominio especificado coincide con `WebpageURL` el valor de una propiedad, la entrega descarga una lista de identificadores de aplicación aprobados del sitio web en ese dominio. El sitio web debe proporcionar una lista de identificadores aprobados en un archivo JSON firmado denominado **Apple-App-site-Association** (por `https://company.com/apple-app-site-association`ejemplo,).

Este archivo JSON contiene un diccionario que especifica una lista de identificadores de aplicación en `<team identifier>.<bundle identifier>`el formulario. Por ejemplo:

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Para firmar el archivo JSON (para `Content-Type` que tenga el correcto de `application/pkcs7-mime`), use la aplicación **terminal** y un `openssl` comando con un certificado y una clave emitidos por una entidad de certificación de confianza de [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012) iOS (consulte para obtener una lista). Por ejemplo:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

El `openssl` comando genera un archivo JSON firmado que se coloca en el sitio web en la dirección URL de la **Asociación de Apple-App-site** . Por ejemplo:

```csharp
https://example.com/apple-app-site-association.
```

La aplicación recibirá cualquier actividad cuyo `WebpageURL` dominio tenga su `com.apple.developer.associated-domains` derecho. Solo se `http` admiten los protocolos y `https` , cualquier otro protocolo producirá una excepción.

## <a name="supporting-handoff-in-document-based-apps"></a>Compatibilidad con la entrega en aplicaciones basadas en documentos

Como se indicó anteriormente, en iOS y OS X, las aplicaciones basadas en documentos admiten automáticamente la entrega de documentos basados en iCloud si el archivo **info. plist** de la `CFBundleDocumentTypes` aplicación contiene `NSUbiquitousDocumentUserActivityType`una clave de. Por ejemplo:

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

En este ejemplo, la cadena es un designador de aplicación DNS inverso con el nombre de la actividad anexada. Si se especifica de esta manera, no es necesario repetir las entradas de tipo de actividad `NSUserActivityTypes` en la matriz del archivo **info. plist** .

Otros objetos de la aplicación pueden hacer referencia al objeto de actividad de `UserActivity` usuario creado automáticamente (disponible a través de la propiedad del documento) y se puede usar para restaurar el estado de continuación. Por ejemplo, para realizar un seguimiento de la selección de elementos y la posición del documento. Debe establecer esta propiedad de actividades `NeedsSave` en `true` siempre que el estado cambie y actualice el `UserInfo` Diccionario en el `UpdateUserActivityState` método.

La `UserActivity` propiedad se puede usar desde cualquier subproceso y se ajusta al Protocolo de valor de clave (KVO), por lo que se puede usar para mantener un documento sincronizado mientras se mueve dentro y fuera de iCloud. La `UserActivity` propiedad se invalidará cuando se cierre el documento.

Para más información, consulte la documentación sobre la compatibilidad de la actividad de usuario de Apple [en aplicaciones basadas en documentos](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) .

## <a name="supporting-handoff-in-responders"></a>Compatibilidad con la entrega en los respondedores

Puede asociar los respondedores (heredados `UIResponder` de en iOS `NSResponder` o en OS X) a las actividades estableciendo `UserActivity` sus propiedades. El sistema guarda automáticamente la `UserActivity` propiedad en el momento adecuado, llamando al método del `UpdateUserActivityState` respondedor para agregar los datos actuales al objeto de actividad del usuario `AddUserInfoEntriesFromDictionary` mediante el método.

## <a name="supporting-continuation-streams"></a>Admitir secuencias de continuación

Puede tratarse de situaciones en las que la carga de entrega inicial no puede transferir eficazmente la cantidad de información necesaria para continuar una actividad. En estas situaciones, la aplicación receptora puede establecer uno o varios flujos entre sí mismo y la aplicación de origen para transferir los datos.

La aplicación de origen establecerá la `SupportsContinuationStreams` propiedad de la `NSUserActivity` instancia en `true`. Por ejemplo:

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

A continuación, la aplicación receptora `GetContinuationStreams` puede llamar al `NSUserActivity` método de `AppDelegate` en su para establecer la secuencia. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

En el dispositivo de origen, el delegado de actividad del usuario recibe los flujos llamando `DidReceiveInputStream` a su método para proporcionar los datos solicitados para continuar con la actividad del usuario en el dispositivo de reanudación.

Usará un `NSInputStream` objeto para proporcionar acceso de solo lectura a los datos de flujo y un `NSOutputStream` proporciona acceso de solo escritura. Los flujos deben usarse en un modo de solicitud y respuesta, donde la aplicación receptora solicita más datos y la aplicación de origen lo proporciona. De este modo, los datos escritos en el flujo de salida en el dispositivo de origen se leen desde el flujo de entrada en el dispositivo que continúa y viceversa.

Incluso en situaciones en las que se requiere una secuencia de continuación, debe haber una mínima comunicación entre las dos aplicaciones.

Para obtener más información, consulte la documentación sobre el [uso de secuencias de continuación](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) de Apple.

## <a name="handoff-best-practices"></a>Procedimientos recomendados de entrega

La implementación correcta de la continuación sin problemas de una actividad de usuario a través de la entrega requiere un cuidadoso diseño debido a todos los componentes implicados. Apple sugiere adoptar las siguientes prácticas recomendadas para las aplicaciones habilitadas para la entrega:

- Diseñe las actividades de usuario para que requieran la carga más pequeña posible para relacionar el estado de la actividad que se va a continuar. Cuanto mayor sea la carga útil, más tiempo tardará en iniciarse la continuación.
- Si debe transferir grandes cantidades de datos para una continuación correcta, tenga en cuenta los costos implicados en la configuración y la sobrecarga de la red.
- Es habitual que una aplicación Mac grande cree actividades de usuario controladas por varias aplicaciones más pequeñas y específicas de tareas en dispositivos iOS. Las distintas versiones de la aplicación y del sistema operativo deben estar diseñadas para funcionar bien de forma conjunta o con errores.
- Al especificar los tipos de actividad, use la notación DNS inversa para evitar colisiones. Si una actividad es específica de una aplicación determinada, su nombre debe incluirse en la definición de tipo (por `com.myCompany.myEditor.editing`ejemplo,). Si la actividad puede trabajar en varias aplicaciones, quite el nombre de la aplicación de la definición ( `com.myCompany.editing`por ejemplo,).
- Si la aplicación necesita actualizar el estado de una actividad de usuario (`NSUserActivity`), establezca `NeedsSave` la propiedad `true`en. En el momento adecuado, la entrega llamará al `UserActivityWillSave` método del delegado para que pueda `UserInfo` actualizar el Diccionario según sea necesario.
- Dado que es posible que el proceso de entrega no se inicialice al instante en el dispositivo `AppDelegate`receptor `WillContinueUserActivity` , debe implementar el y informar al usuario de que la continuación está a punto de iniciarse.

## <a name="example-handoff-app"></a>Ejemplo de aplicación de entrega

Como ejemplo del uso de la entrega en una aplicación de Xamarin. iOS, hemos incluido la aplicación de ejemplo [**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser) con esta guía. La aplicación tiene cuatro pestañas que el usuario puede usar para explorar la web, cada una con un tipo de actividad determinado: Meteorología, favorito, pausa del café y trabajo.

En cualquier pestaña, cuando el usuario escribe una nueva dirección URL y pulsa el botón **ir** , se `NSUserActivity` crea un nuevo para esa pestaña que contiene la dirección URL que el usuario está explorando actualmente:

[![](handoff-images/handoff01.png "Ejemplo de aplicación de entrega")](handoff-images/handoff01.png#lightbox)

Si otro de los dispositivos del usuario tiene instalada la aplicación **MonkeyBrowser** , se inicia sesión en iCloud con la misma cuenta de usuario, está en la misma red y cerca del dispositivo anterior, la actividad de entrega se mostrará en la pantalla principal (en la parte inferior esquina izquierda):

[![](handoff-images/handoff02.png "La actividad de entrega que se muestra en la esquina inferior izquierda de la pantalla principal")](handoff-images/handoff02.png#lightbox)

Si el usuario arrastra hacia arriba en el icono de entrega, se iniciará la aplicación y la actividad de usuario especificada `NSUserActivity` en el se continuará en el nuevo dispositivo:

[![](handoff-images/handoff03.png "La actividad de usuario continuó en el nuevo dispositivo")](handoff-images/handoff03.png#lightbox)

Cuando la actividad de usuario se ha enviado correctamente a otro dispositivo de Apple, el dispositivo `NSUserActivity` de envío recibirá una llamada `UserActivityWasContinued` al método en `NSUserActivityDelegate` su para indicarle que la actividad de usuario se ha transferido correctamente a otro dispositivos.

## <a name="summary"></a>Resumen

En este artículo se ha proporcionado una introducción al marco de entrega que se usa para continuar una actividad de usuario entre varios dispositivos de Apple del usuario. A continuación, se mostró cómo habilitar e implementar la entrega en una aplicación de Xamarin. iOS. Por último, se han explicado los diferentes tipos de continuaciones de entrega disponibles y las prácticas recomendadas de entrega.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Ejemplo de MonkeyBrowser](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [Novedades de iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Directrices de la interfaz de usuario de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Referencia de HomeKit Framework](https://developer.apple.com/library/ios/home_kit_framework_ref)
