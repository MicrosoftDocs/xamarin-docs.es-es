---
title: Usar iCloud con Xamarin. iOS
description: En este documento se describe iCloud y su uso en aplicaciones de Xamarin. iOS. Describe el almacenamiento de clave-valor, el almacenamiento de documentos y la copia de seguridad de iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/09/2016
ms.openlocfilehash: 364775ae8e8874d87022b5e45bd23ea29e82382d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292421"
---
# <a name="using-icloud-with-xamarinios"></a>Usar iCloud con Xamarin. iOS

La API de almacenamiento de iCloud en iOS 5 permite a las aplicaciones guardar documentos de usuario y datos específicos de la aplicación en una ubicación central y acceder a esos elementos desde todos los dispositivos del usuario.

Hay cuatro tipos de almacenamiento disponibles:

- **Almacenamiento de clave-valor** : para compartir pequeñas cantidades de datos con la aplicación en otros dispositivos de usuario.

- **UIDocument Storage** : para almacenar documentos y otros datos en la cuenta de icloud del usuario mediante una subclase de UIDocument.

- **CoreData** : almacenamiento de base de datos SQLite.

- **Archivos y directorios individuales** : para administrar muchos archivos diferentes directamente en el sistema de archivos.

En este documento se describen los dos primeros tipos, pares clave-valor y subclases UIDocument, y cómo usar esas características en Xamarin. iOS.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="requirements"></a>Requisitos

- La versión estable más reciente de Xamarin. iOS
- Xcode 10
- Visual Studio para Mac o Visual Studio 2019.

## <a name="preparing-for-icloud-development"></a>Preparar el desarrollo de iCloud

Las aplicaciones deben configurarse para usar iCloud en el [portal de aprovisionamiento de Apple](https://developer.apple.com/account/ios/overview.action) y en el propio proyecto. Antes de desarrollar para iCloud (o probar los ejemplos), siga estos pasos.

Para configurar correctamente una aplicación para tener acceso a iCloud:

- **Busque** el inicio de sesión de TeamID en [Developer.Apple.com](https://developer.apple.com) y visite el **centro de usuarios > su cuenta > Resumen** de la cuenta de desarrollador para obtener su identificador de equipo (o un identificador individual para desarrolladores únicos). Será una cadena de 10 caracteres ( **A93A5CM278** por ejemplo), que forma parte del "identificador de contenedor".

- **Crear un nuevo identificador de aplicación** : para crear un identificador de aplicación, siga los pasos descritos en la [sección aprovisionamiento de tecnologías de la tienda de la guía de aprovisionamiento de dispositivos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)y asegúrese de comprobar **iCloud** como servicio permitido:

 [![](introduction-to-icloud-images/icloud-sml.png "Comprobar iCloud como servicio permitido")](introduction-to-icloud-images/icloud.png#lightbox)

- **Crear un nuevo perfil de aprovisionamiento** : para crear un perfil de aprovisionamiento, siga los pasos descritos en la [Guía de aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Agregue el identificador del contenedor a contitles. plist** : el formato del identificador del contenedor es `TeamID.BundleID`. Para obtener más información, consulte la guía [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) .

- **Configurar las propiedades del proyecto** : en el archivo info. plist, asegúrese de que el **identificador de paquete** coincide con el **identificador de paquete** establecido al [crear un identificador de aplicación](~/ios/deploy-test/provisioning/capabilities/index.md). La firma del lote de iOS usa un **Perfil de aprovisionamiento** que contiene un identificador de aplicación con el App Service iCloud y el archivo de **derechos personalizados** seleccionado. Todo esto se puede hacer en Visual Studio en el panel Propiedades del proyecto.

- **Habilitar icloud en el dispositivo** : vaya a **Configuración > iCloud** y asegúrese de que el dispositivo está conectado.
Seleccione y active la opción **documentos & datos** .

- **Debe usar un dispositivo para probar iCloud** ; no funcionará en el simulador.
De hecho, en realidad se necesitan dos o más dispositivos que han iniciado sesión con el mismo ID de Apple para ver iCloud en acción.


## <a name="key-value-storage"></a>Almacenamiento de clave-valor

El almacenamiento de clave-valor está diseñado para pequeñas cantidades de datos que un usuario podría desear conservar en los dispositivos, como la última página que se vio en un libro o revista. No se debe usar el almacenamiento de clave-valor para los datos de copia de seguridad.

Hay algunas limitaciones que se deben tener en cuenta al usar el almacenamiento de valor de clave:

- **Tamaño de clave máximo** : los nombres de clave no pueden tener más de 64 bytes.

- **Tamaño máximo del valor** : no puede almacenar más de 64 kilobytes en un solo valor.

- **Tamaño máximo de almacenamiento de clave-valor para una aplicación** : las aplicaciones solo pueden almacenar hasta 64 kilobytes de datos de valor de clave en total. Se producirá un error en los intentos de establecer claves más allá de ese límite y se conservará el valor anterior.

- **Tipos de datos** : solo se pueden almacenar tipos básicos como cadenas, números y valores booleanos.

En el ejemplo **iCloudKeyValue** se muestra cómo funciona. En el código de ejemplo se crea una clave denominada para cada dispositivo: puede establecer esta clave en un dispositivo y ver el valor obtener propagado a otros usuarios. También crea una clave denominada "compartida" que se puede editar en cualquier dispositivo. Si edita muchos dispositivos a la vez, iCloud decidirá qué valor "gana" (usando una marca de tiempo en el cambio) y se propagará.

En esta captura de pantalla se muestra el ejemplo en uso. Cuando se reciben notificaciones de cambios de iCloud, se imprimen en la vista de texto desplazable en la parte inferior de la pantalla y se actualizan en los campos de entrada.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "El flujo de mensajes entre dispositivos")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Establecer y recuperar datos

Este código muestra cómo establecer un valor de cadena.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

La llamada a Synchronize garantiza que el valor se conserva en el almacenamiento en disco local únicamente. La sincronización con iCloud se produce en segundo plano y el código de aplicación no puede "forzarla". Con una buena conectividad de red, la sincronización se realiza a menudo en 5 segundos, sin embargo, si la red es deficiente (o está desconectada), una actualización puede tardar mucho más.

Puede recuperar un valor con este código:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

El valor se recupera del almacén de datos local: este método no intenta ponerse en contacto con los servidores de iCloud para obtener el valor "latest". iCloud actualizará el almacén de datos local según su propia programación.

### <a name="deleting-data"></a>Eliminar datos

Para quitar completamente un par clave-valor, use el método Remove de la siguiente manera:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Observar cambios

Una aplicación también puede recibir notificaciones cuando iCloud cambia los valores agregando un observador a `NSNotificationCenter.DefaultCenter`.
El siguiente código del método **KeyValueViewController.CS** `ViewWillAppear` muestra cómo realizar escuchas para esas notificaciones y crear una lista de las claves que se han cambiado:

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

Después, el código puede realizar alguna acción con la lista de claves cambiadas, como actualizar una copia local de ellas o actualizar la interfaz de usuario con los nuevos valores.

Las posibles razones de cambio son: ServerChange (0), InitialSyncChange (1) o QuotaViolationChange (2). Puede tener acceso al motivo y realizar un procesamiento diferente si es necesario (por ejemplo, puede que necesite quitar algunas claves como resultado de un *QuotaViolationChange*).

## <a name="document-storage"></a>Almacenamiento de documentos

el almacenamiento de documentos de iCloud está diseñado para administrar los datos que son importantes para la aplicación (y para el usuario). Se puede usar para administrar archivos y otros datos que la aplicación necesita para ejecutarse, al mismo tiempo que proporciona funciones de copia de seguridad y uso compartido basadas en iCloud en todos los dispositivos del usuario.

En este diagrama se muestra cómo encaja todo. Cada dispositivo tiene datos guardados en el almacenamiento local (UbiquityContainer) y el demonio de iCloud del sistema operativo se encarga de enviar y recibir datos en la nube. Todo el acceso a los archivos de UbiquityContainer debe realizarse a través de FilePresenter/FileCoordinator para evitar el acceso simultáneo. La `UIDocument` clase las implementa para usted; en este ejemplo se muestra cómo usar UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "Información general de almacenamiento de documentos")](introduction-to-icloud-images/icloud-overview.png#lightbox)

En el ejemplo iCloudUIDoc se implementa una `UIDocument` subclase simple que contiene un solo campo de texto. El texto se representa en una `UITextView` y las ediciones se propagan mediante iCloud a otros dispositivos con un mensaje de notificación que aparece en rojo. En el código de ejemplo no se tratan características de iCloud más avanzadas, como la resolución de conflictos.

En esta captura de pantalla se muestra la aplicación de ejemplo: después de cambiar el texto y presionar **UpdateChangeCount** , el documento se sincroniza a través de icloud con otros dispositivos.

 [![](introduction-to-icloud-images/iclouduidoc.png "En esta captura de pantalla se muestra la aplicación de ejemplo después de cambiar el texto y presionar UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Hay cinco partes en el ejemplo iCloudUIDoc:

1. **Acceder a UbiquityContainer** : Determine si iCloud está habilitado y, si es así, la ruta de acceso al área de almacenamiento de icloud de la aplicación.

1. **Crear una subclase UIDocument** : cree una clase para intermediar entre el almacenamiento de icloud y los objetos del modelo.

1. **Buscar y abrir documentos de icloud** : `NSFileManager` use `NSPredicate` y para buscar documentos de icloud y abrirlos.

1. **Mostrar documentos de icloud** : exponga las propiedades `UIDocument` de para que pueda interactuar con los controles de interfaz de usuario.

1. **Guardar documentos de icloud** : Asegúrese de que los cambios realizados en la interfaz de usuario se guardan en el disco y en iCloud.

Todas las operaciones de iCloud se ejecutan (o se deben ejecutar) de forma asincrónica para que no se bloqueen mientras esperan que suceda algo. Verá tres maneras diferentes de lograrlo en el ejemplo:

 **Subprocesos** : en `AppDelegate.FinishedLaunching` la llamada `GetUrlForUbiquityContainer` inicial a, se realiza en otro subproceso para evitar el bloqueo del subproceso principal.

 **NotificationCenter** : registro para las notificaciones cuando se `NSMetadataQuery.StartQuery` completan las operaciones asincrónicas.

 **Controladores de finalización** : pasar métodos para que se ejecuten al finalizar operaciones asincrónicas como `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Acceso a UbiquityContainer

El primer paso en el uso del almacenamiento de documentos de iCloud es determinar si iCloud está habilitado y, en ese caso, la ubicación del "contenedor ubicuidad" (el directorio donde se almacenan los archivos habilitados para iCloud en el dispositivo).

Este código está en el `AppDelegate.FinishedLaunching` método del ejemplo.

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

Aunque el ejemplo no lo hace, Apple recomienda llamar a GetUrlForUbiquityContainer siempre que una aplicación llegue al primer plano.

### <a name="creating-a-uidocument-subclass"></a>Crear una subclase UIDocument

Todos los archivos y directorios de iCloud (es decir, cualquier elemento almacenado en el directorio UbiquityContainer) se deben administrar mediante métodos NSFileManager, implementando el protocolo NSFilePresenter y escribiendo mediante un NSFileCoordinator.
La manera más sencilla de hacer todo esto es no escribirlo usted mismo, sino subclase UIDocument, que lo hace todo por usted.

Hay solo dos métodos que se deben implementar en una subclase UIDocument para que funcionen con iCloud:

- **LoadFromContents** : pasa el NSData del contenido del archivo para desempaquetar en las clases de modelo.

- **ContentsForType** : le pide que proporcione la representación NSData de la clase de modelo/es para guardarla en el disco (y en la nube).

Este código de ejemplo de **iCloudUIDoc\MonkeyDocument.CS** muestra cómo implementar UIDocument.

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

En este caso, el modelo de datos es muy simple: un solo campo de texto. El modelo de datos puede ser tan complejo como sea necesario, como un documento XML o datos binarios. El rol principal de la implementación de UIDocument es traducir entre las clases del modelo y una representación NSData que se puede guardar o cargar en el disco.

### <a name="finding-and-opening-icloud-documents"></a>Buscar y abrir documentos de iCloud

La aplicación de ejemplo solo trata con un único archivo-test. txt, por lo que el código de `NSPredicate` AppDelegate.CS `NSMetadataQuery` crea un y para buscar específicamente ese nombre de archivo. El `NSMetadataQuery` se ejecuta de forma asincrónica y envía una notificación cuando finaliza. `DidFinishGathering`lo llama el observador de notificación, detiene la consulta y llama a LoadDocument, que usa `UIDocument.Open` el método con un controlador de finalización para intentar cargar el archivo y mostrarlo `MonkeyDocumentViewController`en un.

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>Mostrar documentos de iCloud

Mostrar un UIDocument no debe ser diferente a cualquier otra clase de modelo
- las propiedades se muestran en los controles de IU, posiblemente editados por el usuario y, a continuación, se escriben de nuevo en el modelo.

En el ejemplo **iCloudUIDoc\MonkeyDocumentViewController.CS** se muestra el texto de MonkeyDocument `UITextView`en un. `ViewDidLoad`Escucha la notificación enviada en el `MonkeyDocument.LoadFromContents` método. `LoadFromContents`se llama a cuando iCloud tiene nuevos datos para el archivo, de modo que la notificación indica que el documento se ha actualizado.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

El controlador de notificación de código de ejemplo llama a un método para actualizar la interfaz de usuario en este caso sin ninguna detección ni resolución de conflictos.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Guardar documentos de iCloud

Para agregar un UIDocument a iCloud, puede llamar `UIDocument.Save` directamente a (solo para nuevos documentos) o bien, para trasladar un archivo existente mediante. `NSFileManager.DefaultManager.SetUbiquitious` En el código de ejemplo se crea un nuevo documento directamente en el contenedor ubicuidad con este código (hay dos controladores de finalización aquí, `Save` uno para la operación y otro para el abierto):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

Los cambios posteriores en el documento no se "guardan" directamente; en su lugar `UIDocument` , se indica al que `UpdateChangeCount`ha cambiado con y se programará automáticamente una operación de guardar en el disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Administración de documentos de iCloud

Los usuarios pueden administrar los documentos de iCloud en el directorio **documentos** del "contenedor ubicuidad" fuera de la aplicación mediante la configuración; pueden ver la lista de archivos y pasar el dedo a la eliminación. El código de aplicación debe ser capaz de controlar la situación en la que el usuario elimina los documentos. No almacene datos internos de aplicaciones en el directorio **documentos** .

 [![](introduction-to-icloud-images/icloudstorage.png "Flujo de trabajo de administración de documentos iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Los usuarios también recibirán advertencias diferentes cuando intenten quitar una aplicación habilitada para iCloud de su dispositivo, para informarles del estado de los documentos de iCloud relacionados con la aplicación.

 [![](introduction-to-icloud-images/icloud-delete1.png "Cuadro de diálogo de ejemplo cuando el usuario intenta quitar una aplicación habilitada para iCloud de su dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Cuadro de diálogo de ejemplo cuando el usuario intenta quitar una aplicación habilitada para iCloud de su dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Copia de seguridad de iCloud

Aunque la copia de seguridad en iCloud no es una característica a la que los desarrolladores acceden directamente, la forma en que diseña la aplicación puede afectar a la experiencia del usuario.
Apple proporciona [directrices de almacenamiento de datos de iOS](https://developer.apple.com/icloud/documentation/data-storage/) para que los desarrolladores puedan seguir en sus aplicaciones de iOS.

La consideración más importante es si la aplicación almacena archivos grandes que no son generados por el usuario (por ejemplo, una aplicación de lector de revista que almacena cientos más megabytes de contenido por problema). Apple prefiere que no almacene este tipo de datos donde se realizará la copia de seguridad en iCloud y rellene innecesariamente la cuota de iCloud del usuario.

Las aplicaciones que almacenan grandes cantidades de datos de este tipo deben almacenarse en uno de los directorios de usuario de los que no se ha realizado ninguna copia de seguridad (por ejemplo, Almacena en caché o tmp) o `NSFileManager.SetSkipBackupAttribute` usar para aplicar una marca a esos archivos para que iCloud los omita durante las operaciones de copia de seguridad.

## <a name="summary"></a>Resumen

En este artículo se presentó la nueva característica de iCloud incluida en iOS 5. Se han examinado los pasos necesarios para configurar el proyecto para usar iCloud y, a continuación, se proporcionan ejemplos de cómo implementar las características de iCloud.

El ejemplo de almacenamiento de valor de clave muestra cómo se puede usar iCloud para almacenar una pequeña cantidad de datos similar a la forma en que se almacenan los NSUserPreferences. En el ejemplo UIDocument se muestra cómo se pueden almacenar y sincronizar datos más complejos en varios dispositivos a través de iCloud.

Por último, incluye una breve explicación sobre cómo la incorporación de copias de seguridad de iCloud debe influir en el diseño de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iCloud (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introductiontoicloud)
- [Código de ejemplo del Seminario de iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Diapositivas del Seminario de iCloud](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Almacenamiento de iCloud](https://support.apple.com/kb/HT4847)
