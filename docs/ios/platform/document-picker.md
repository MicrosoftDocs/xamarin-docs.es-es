---
title: Selector de documentos en Xamarin. iOS
description: En este documento se describe el selector de documentos de iOS y se describe cómo usarlo en Xamarin. iOS. Se presenta un vistazo a iCloud, documentos, código de instalación común, extensiones de proveedor de documentos y mucho más.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: bbb38acdb3de972cd7f2e2ee04233bf7ed88897a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032587"
---
# <a name="document-picker-in-xamarinios"></a>Selector de documentos en Xamarin. iOS

El selector de documentos permite que los documentos se compartan entre las aplicaciones. Estos documentos pueden almacenarse en iCloud o en otro directorio de la aplicación. Los documentos se comparten mediante el conjunto de [extensiones de proveedor de documentos](~/ios/platform/extensions.md) que el usuario ha instalado en su dispositivo. 

Debido a la dificultad de mantener los documentos sincronizados entre las aplicaciones y la nube, estos presentan una determinada cantidad de complejidad necesaria.

## <a name="requirements"></a>Requisitos

Para completar los pasos que se describen en este artículo, es necesario lo siguiente:

- **Xcode 7 e iOS 8 o posterior** : las API Xcode 7 e iOS 8 de Apple o las API más recientes deben instalarse y configurarse en el equipo del desarrollador.
- **Visual Studio o Visual Studio para Mac** : se debe instalar la versión más reciente de Visual Studio para Mac.
- **dispositivo iOS** : un dispositivo iOS que ejecuta iOS 8 o posterior.

## <a name="changes-to-icloud"></a>Cambios en iCloud

Para implementar las nuevas características del selector de documentos, se han realizado los siguientes cambios en el servicio iCloud de Apple:

- El demonio de iCloud se ha reescrito por completo con CloudKit.
- Se ha cambiado el nombre de la unidad iCloud a las características existentes de iCloud.
- Se ha agregado compatibilidad con el sistema operativo Microsoft Windows a iCloud.
- Se ha agregado una carpeta iCloud en el buscador de Mac OS.
- los dispositivos iOS pueden acceder al contenido de la carpeta Mac OS iCloud.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="what-is-a-document"></a>¿Qué es un documento?

Al hacer referencia a un documento en iCloud, se trata de una entidad única e independiente que el usuario debe percibir como tal. Es posible que un usuario desee modificar el documento o compartirlo con otros usuarios (por ejemplo, mediante correo electrónico).

Hay varios tipos de archivos que el usuario reconocerá inmediatamente como documentos, como páginas, discurso o archivos de números. Sin embargo, iCloud no se limita a este concepto. Por ejemplo, el estado de un juego (como una coincidencia de ajedrez) se puede tratar como un documento y almacenarse en iCloud. Este archivo se puede pasar entre los dispositivos de un usuario y permitirles seleccionar un juego en el que se dejaron en un dispositivo diferente.

## <a name="dealing-with-documents"></a>Trabajar con documentos

Antes de profundizar en el código necesario para usar el selector de documentos con Xamarin, este artículo tratará los procedimientos recomendados para trabajar con documentos de iCloud y algunas de las modificaciones realizadas en las API existentes necesarias para admitir el selector de documentos.

### <a name="using-file-coordination"></a>Usar la coordinación de archivos

Dado que un archivo se puede modificar desde varias ubicaciones diferentes, se debe usar la coordinación para evitar la pérdida de datos.

 [![](document-picker-images/image1.png "Using File Coordination")](document-picker-images/image1.png#lightbox)

Echemos un vistazo a la ilustración anterior:

1. Un dispositivo iOS que usa la coordinación de archivos crea un nuevo documento y lo guarda en la carpeta iCloud.
2. iCloud guarda el archivo modificado en la nube para su distribución en todos los dispositivos.
3. Un equipo Mac conectado Ve el archivo modificado en la carpeta iCloud y usa la coordinación de archivos para copiar los cambios en el archivo.
4. Un dispositivo que no usa la coordinación de archivos realiza un cambio en el archivo y lo guarda en la carpeta iCloud. Estos cambios se replican al instante en los demás dispositivos.

Supongamos que el dispositivo iOS original o el equipo Mac estaba editando el archivo, ahora se pierden los cambios y se sobrescriben con la versión del archivo del dispositivo no coordinado. Para evitar la pérdida de datos, la coordinación de archivos es un necesario al trabajar con documentos basados en la nube.

### <a name="using-uidocument"></a>Usar UIDocument

 `UIDocument` simplifica las cosas (o `NSDocument` en macOS) al realizar todo el trabajo pesado del desarrollador. Proporciona coordinación de archivos integrada con colas en segundo plano para evitar que bloquee la interfaz de usuario de la aplicación.

 `UIDocument` expone varias API de alto nivel que facilitan el esfuerzo de desarrollo de una aplicación de Xamarin para cualquier propósito que requiera el desarrollador.

En el código siguiente se crea una subclase de `UIDocument` para implementar un documento genérico basado en texto que se puede usar para almacenar y recuperar texto de iCloud:

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

La clase `GenericTextDocument` presentada anteriormente se usará en este artículo al trabajar con el selector de documentos y los documentos externos en una aplicación Xamarin. iOS 8.

## <a name="asynchronous-file-coordination"></a>Coordinación asincrónica de archivos

iOS 8 proporciona varias nuevas características de coordinación de archivos asincrónicas a través de las nuevas API de coordinación de archivos. Antes de iOS 8, todas las API de coordinación de archivos existentes eran totalmente sincrónicas. Esto significaba que el desarrollador era responsable de implementar su propia cola de mensajes para impedir que la coordinación de archivos bloqueara la interfaz de usuario de la aplicación.

La nueva clase `NSFileAccessIntent` contiene una dirección URL que apunta al archivo y varias opciones para controlar el tipo de coordinación requerido. En el código siguiente se muestra cómo mover un archivo de una ubicación a otra mediante el uso de intenciones:

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>Detección y enumeración de documentos

La manera de detectar y enumerar documentos es mediante el uso de las API de `NSMetadataQuery` existentes. En esta sección se tratarán las nuevas características agregadas a `NSMetadataQuery` que hacen que el trabajo con documentos sea aún más sencillo.

### <a name="existing-behavior"></a>Comportamiento existente

Antes de iOS 8, `NSMetadataQuery` era lenta para recoger los cambios de archivos locales, como eliminaciones, crea y cambia el nombre.

 [![](document-picker-images/image2.png "NSMetadataQuery local file changes overview")](document-picker-images/image2.png#lightbox)

En el diagrama anterior:

1. En el caso de los archivos que ya existen en el contenedor de la aplicación, `NSMetadataQuery` tiene registros de `NSMetadata` existentes creados previamente y puestos en cola para que estén disponibles al instante en la aplicación.
1. La aplicación crea un nuevo archivo en el contenedor de la aplicación.
1. Hay un retraso antes de que `NSMetadataQuery` vea la modificación en el contenedor de la aplicación y cree el registro de `NSMetadata` requerido.

Debido al retraso en la creación del registro de `NSMetadata`, la aplicación debía tener dos orígenes de datos abiertos: uno para los cambios de archivos locales y otro para los cambios basados en la nube.

### <a name="stitching"></a>Unión

En iOS 8, `NSMetadataQuery` es más fácil de usar directamente con una nueva característica denominada Unión:

 [![](document-picker-images/image3.png "NSMetadataQuery with a new feature called Stitching")](document-picker-images/image3.png#lightbox)

Usar la Unión en el diagrama anterior:

1. Como antes, en el caso de los archivos que ya existen en el contenedor de la aplicación, `NSMetadataQuery` tiene registros de `NSMetadata` existentes creados previamente y puestos en cola.
1. La aplicación crea un nuevo archivo en el contenedor de la aplicación mediante la coordinación de archivos.
1. Un enlace en el contenedor de la aplicación ve la modificación y llama a `NSMetadataQuery` para crear el registro de `NSMetadata` requerido.
1. El registro de `NSMetadata` se crea directamente después del archivo y se pone a disposición de la aplicación.

Al usar la Unión de la aplicación ya no tiene que abrir un origen de datos para supervisar los cambios en los archivos locales y basados en la nube. Ahora la aplicación puede confiar en `NSMetadataQuery` directamente.

> [!IMPORTANT]
> La Unión solo funciona si la aplicación usa la coordinación de archivos, tal y como se muestra en la sección anterior. Si no se utiliza la coordinación de archivos, las API usan de forma predeterminada el comportamiento anterior de iOS 8 existente.

### <a name="new-ios-8-metadata-features"></a>Nuevas características de metadatos de iOS 8

Se han agregado las siguientes características nuevas a `NSMetadataQuery` en iOS 8:

- ahora `NSMetatadataQuery` puede mostrar los documentos no locales almacenados en la nube.
- Se han agregado nuevas API para tener acceso a la información de metadatos en los documentos basados en la nube. 
- Hay una nueva API de `NSUrl_PromisedItems` que tendrá acceso a los atributos de archivo de los archivos que pueden o no tener su contenido disponible localmente.
- Use el método `GetPromisedItemResourceValue` para obtener información sobre un archivo determinado o usar el método `GetPromisedItemResourceValues` para obtener información sobre más de un archivo a la vez.

Se han agregado dos nuevas marcas de coordinación de archivos para trabajar con los metadatos:

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
- `NSFileCoordinatorWriteContentIndependentMetadataOnly` 

Con las marcas anteriores, no es necesario que el contenido del archivo de documento esté disponible localmente para que se usen.

En el segmento de código siguiente se muestra cómo usar `NSMetadataQuery` para consultar la existencia de un archivo específico y compilar el archivo si no existe:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),             NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>Miniaturas de documentos

Apple cree que la mejor experiencia de usuario al enumerar los documentos de una aplicación es usar las versiones preliminares. Esto proporciona el contexto de los usuarios finales, por lo que puede identificar rápidamente el documento con el que desea trabajar.

Antes de iOS 8, la visualización de las vistas previas de documento requería una implementación personalizada. Novedad de iOS 8 son atributos del sistema de archivos que permiten al desarrollador trabajar rápidamente con miniaturas de documentos.

#### <a name="retrieving-document-thumbnails"></a>Recuperar miniaturas de documentos 

Al llamar a los métodos `GetPromisedItemResourceValue` o `GetPromisedItemResourceValues`, se devuelve `NSUrl_PromisedItems` API, una `NSUrlThumbnailDictionary`. La única clave actualmente en este diccionario es la `NSThumbnial1024X1024SizeKey` y su `UIImage`coincidente.

#### <a name="saving-document-thumbnails"></a>Guardar miniaturas de documentos

La forma más fácil de guardar una miniatura es mediante `UIDocument`. Al llamar al método `GetFileAttributesToWrite` del `UIDocument` y establecer la miniatura, se guardará automáticamente cuando el archivo de documento sea. El demonio de iCloud verá este cambio y lo propagará a iCloud. En Mac OS X, el complemento Quick Look genera automáticamente las miniaturas para el desarrollador.

Con los aspectos básicos del trabajo con documentos basados en iCloud, junto con las modificaciones de la API existente, estamos preparados para implementar el controlador de vista del selector de documentos en una aplicación móvil de Xamarin iOS 8.

## <a name="enabling-icloud-in-xamarin"></a>Habilitar iCloud en Xamarin

Antes de que el selector de documentos se pueda usar en una aplicación de Xamarin. iOS, la compatibilidad con iCloud debe estar habilitada tanto en la aplicación como a través de Apple. 

Los pasos siguientes le guiarán en el proceso de aprovisionamiento de iCloud.

1. Cree un contenedor de iCloud.
2. Cree un identificador de aplicación que contenga el App Service iCloud.
3. Cree un perfil de aprovisionamiento que incluya este identificador de aplicación.

La guía [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) le guía a través de los dos primeros pasos. Para crear un perfil de aprovisionamiento, siga los pasos descritos en la guía de [Perfil de aprovisionamiento](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

Los pasos siguientes le guiarán en el proceso de configuración de la aplicación para iCloud:

Haga lo siguiente:

1. Abra el proyecto en Visual Studio para Mac o Visual Studio.
2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione Opciones.
3. En el cuadro de diálogo Opciones, seleccione **aplicación de iOS**, asegúrese de que el **identificador de paquete** coincide con el que se definió en ID. de **aplicación** creado anteriormente para la aplicación. 
4. Seleccione **firma de lote de iOS**, seleccione la **identidad del desarrollador** y el **Perfil de aprovisionamiento** que creó anteriormente.
5. Haga clic en el botón **Aceptar** para guardar los cambios y cerrar el cuadro de diálogo.
6. Haga clic con el botón derecho en `Entitlements.plist` en el **Explorador de soluciones** para abrirlo en el editor.

    > [!IMPORTANT]
    > En Visual Studio, puede que tenga que abrir el editor de derechos haciendo clic con el botón derecho en él y seleccionando **abrir con.** . y seleccionar el editor de lista de propiedades

7. Active **Habilitar iCloud** , **documentos de icloud** , **almacenamiento de clave-valor** y **CloudKit** .
8. Asegúrese de que el **contenedor** existe para la aplicación (como se creó anteriormente). Ejemplo: `iCloud.com.your-company.AppName`
9. Guarde los cambios en el archivo.

Para obtener más información sobre los derechos, consulte la guía [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) .

Con la configuración anterior en su lugar, la aplicación puede usar ahora documentos basados en la nube y el nuevo controlador del selector de documentos.

## <a name="common-setup-code"></a>Código de instalación común

Antes de empezar a usar el controlador de vista del selector de documentos, se requiere un código de configuración estándar. Empiece por modificar el archivo de `AppDelegate.cs` de la aplicación y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                 new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }

        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");    

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {    
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> En el código anterior se incluye el código de la sección anterior detección y enumeración de documentos. Se presenta aquí en su totalidad, tal y como aparecería en una aplicación real. Para simplificar, este ejemplo funciona con un solo archivo codificado de forma rígida (`test.txt`).

El código anterior expone varios métodos abreviados de unidad de iCloud para facilitar el trabajo en el resto de la aplicación.

Después, agregue el código siguiente a cualquier contenedor de vistas o vistas que vaya a usar el selector de documentos o que trabaje con documentos basados en la nube:

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Esto agrega un acceso directo para acceder al `AppDelegate` y obtener acceso a los métodos abreviados de iCloud creados anteriormente.

Con este código en su lugar, echemos un vistazo a la implementación del controlador de vista selector de documentos en una aplicación Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Usar el controlador de vista selector de documentos

Antes de iOS 8, era muy difícil acceder a los documentos desde otra aplicación, ya que no había forma de detectar documentos fuera de la aplicación desde dentro de la aplicación.

### <a name="existing-behavior"></a>Comportamiento existente

 [![](document-picker-images/image31.png "Existing Behavior overview")](document-picker-images/image31.png#lightbox)

Echemos un vistazo a cómo acceder a un documento externo antes de iOS 8:

1. En primer lugar, el usuario tendría que abrir la aplicación que creó originalmente el documento.
1. El documento se selecciona y el `UIDocumentInteractionController` se usa para enviar el documento a la nueva aplicación.
1. Por último, se coloca una copia del documento original en el contenedor de la nueva aplicación.

Desde allí, el documento está disponible para que la segunda aplicación se abra y edite.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Detectar documentos fuera del contenedor de una aplicación

En iOS 8, una aplicación puede obtener acceso a documentos fuera de su propio contenedor de aplicaciones con facilidad:

 [![](document-picker-images/image32.png "Discovering Documents Outside of an App's Container")](document-picker-images/image32.png#lightbox)

Mediante el nuevo selector de documentos de iCloud (`UIDocumentPickerViewController`), una aplicación de iOS puede detectar directamente el contenedor de la aplicación y acceder a él. El `UIDocumentPickerViewController` proporciona un mecanismo para que el usuario conceda acceso y edite esos documentos detectados a través de permisos.

Una aplicación debe participar en que los documentos aparezcan en el selector de documentos de iCloud y estar disponible para que otras aplicaciones puedan detectarlos y trabajar con ellos. Para que una aplicación de Xamarin iOS 8 comparta su contenedor de aplicaciones, edítela `Info.plist` archivo en un editor de texto estándar y agregue las dos líneas siguientes a la parte inferior del diccionario (entre las etiquetas de `<dict>...</dict>`):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

El `UIDocumentPickerViewController` proporciona una excelente interfaz de usuario que permite al usuario elegir documentos. Para mostrar el controlador de vista selector de documentos en una aplicación Xamarin iOS 8, haga lo siguiente:

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> El desarrollador debe llamar al método `StartAccessingSecurityScopedResource` del `NSUrl` antes de que se pueda tener acceso a un documento externo. Se debe llamar al método `StopAccessingSecurityScopedResource` para liberar el bloqueo de seguridad en cuanto se cargue el documento.

### <a name="sample-output"></a>Resultados del ejemplo

Este es un ejemplo de cómo el código anterior mostraría un selector de documentos cuando se ejecutaba en un dispositivo iPhone:

1. El usuario inicia la aplicación y se muestra la interfaz principal:   

    [![](document-picker-images/image33.png "The main interface is displayed")](document-picker-images/image33.png#lightbox)
1. El usuario pulsa el botón de **acción** en la parte superior de la pantalla y se le pide que seleccione un **proveedor de documentos** en la lista de proveedores disponibles:   

    [![](document-picker-images/image34.png "Select a Document Provider from the list of available providers")](document-picker-images/image34.png#lightbox)
1. El **controlador de vista del selector de documentos** se muestra para el **proveedor de documentos**seleccionado:   

    [![](document-picker-images/image35.png "The Document Picker View Controller is displayed")](document-picker-images/image35.png#lightbox)
1. El usuario puntea en una **carpeta de documentos** para mostrar su contenido:   

    [![](document-picker-images/image36.png "The Document Folder contents")](document-picker-images/image36.png#lightbox)
1. El usuario selecciona un **documento** y el **selector de documentos** está cerrado.
1. Se vuelve a mostrar la interfaz principal, el **documento** se carga desde el contenedor externo y se muestra su contenido.

La presentación real del controlador del selector de documentos depende de los proveedores de documentos que el usuario haya instalado en el dispositivo y del modo de selector de documentos que se haya implementado. En el ejemplo anterior se usa el modo Open, los otros tipos de modo se tratarán en detalle a continuación.

## <a name="managing-external-documents"></a>Administrar documentos externos

Como se explicó anteriormente, antes de iOS 8, una aplicación solo podía tener acceso a documentos que formaban parte de su contenedor de aplicaciones. En iOS 8, una aplicación puede acceder a documentos desde orígenes externos:

 [![](document-picker-images/image37.png "Managing External Documents overview")](document-picker-images/image37.png#lightbox)

Cuando el usuario selecciona un documento de un origen externo, se escribe un documento de referencia en el contenedor de la aplicación que señala al documento original.

Para ayudar a agregar esta nueva capacidad a las aplicaciones existentes, se han agregado varias características nuevas a la API de `NSMetadataQuery`. Normalmente, una aplicación usa el ámbito de documento omnipresente para enumerar los documentos que se encuentran en el contenedor de la aplicación. Con este ámbito, solo se seguirán mostrando los documentos dentro del contenedor de la aplicación.

El uso del nuevo ámbito de documento externo omnipresente devolverá los documentos que se encuentren fuera del contenedor de la aplicación y devolverán los metadatos para ellos. El `NSMetadataItemUrlKey` apuntará a la dirección URL donde se encuentra realmente el documento.

A veces, una aplicación no desea trabajar con los documentos a los que apunta la referencia. En su lugar, la aplicación desea trabajar directamente con el documento de referencia. Por ejemplo, es posible que la aplicación desee mostrar el documento en la carpeta de la aplicación en la interfaz de usuario, o bien permitir que el usuario mueva las referencias en una carpeta.

En iOS 8, se ha proporcionado un nuevo `NSMetadataItemUrlInLocalContainerKey` para tener acceso directamente al documento de referencia. Esta clave apunta a la referencia real al documento externo de un contenedor de la aplicación.

El `NSMetadataUbiquitousItemIsExternalDocumentKey` se usa para comprobar si un documento es externo al contenedor de una aplicación. El `NSMetadataUbiquitousItemContainerDisplayNameKey` se utiliza para tener acceso al nombre del contenedor que aloja la copia original de un documento externo.

### <a name="why-document-references-are-required"></a>Por qué se requieren referencias de documento

La razón principal por la que iOS 8 usa referencias para acceder a documentos externos es la seguridad. No se concede acceso a ninguna aplicación a ningún contenedor de otra aplicación. Solo el selector de documentos puede hacerlo, porque se está ejecutando fuera de proceso y tiene acceso a todo el sistema.

La única manera de llegar a un documento fuera del contenedor de la aplicación es mediante el selector de documentos y si la dirección URL devuelta por el selector tiene un ámbito de seguridad. La dirección URL de ámbito de seguridad contiene suficiente información para seleccionar el documento junto con los derechos de ámbito necesarios para conceder a una aplicación acceso al documento.

Es importante tener en cuenta que si la dirección URL de ámbito de seguridad se serializó en una cadena y, a continuación, se deserializa, se perdería la información de seguridad y no se puede obtener acceso al archivo desde la dirección URL. La característica de referencia de documento proporciona un mecanismo para volver a los archivos a los que apuntan estas direcciones URL.

Por tanto, si la aplicación adquiere una `NSUrl` de uno de los documentos de referencia, ya tiene el ámbito de seguridad adjunto y se puede usar para obtener acceso al archivo. Por esta razón, se recomienda encarecidamente que el desarrollador use `UIDocument` porque controla toda esta información y procesos para ellos.

### <a name="using-bookmarks"></a>Utilizar marcadores

No siempre es factible enumerar los documentos de una aplicación para volver a un documento concreto, por ejemplo, al realizar la restauración del estado. iOS 8 proporciona un mecanismo para crear marcadores que se dirigen directamente a un documento determinado.

El código siguiente creará un marcador a partir de la propiedad `FileUrl` de un `UIDocument`:

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

La API de marcador existente se usa para crear un marcador en un `NSUrl` existente que se puede guardar y cargar para proporcionar acceso directo a un archivo externo. En el código siguiente se restaurará un marcador que se creó anteriormente:

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>Abrir frente al modo de importación y al selector de documentos

El controlador de vista del selector de documentos presenta dos modos diferentes de funcionamiento:

1. **Modo de apertura** : en este modo, cuando el usuario selecciona un documento externo, el selector de documentos creará un marcador de ámbito de seguridad en el contenedor de la aplicación.   

    [![](document-picker-images/image37.png "A Security Scoped Bookmark in the Application Container")](document-picker-images/image37.png#lightbox)
1. **Modo de importación** : en este modo, cuando el usuario selecciona un documento externo, el selector de documentos no creará un marcador, sino que, en su lugar, copiará el archivo en una ubicación temporal y proporcionará acceso a la aplicación al documento en esta ubicación:   

    [![](document-picker-images/image38.png "The Document Picker will copy the file into a Temporary Location and provide the application access to the Document at this location")](document-picker-images/image38.png#lightbox)   
 Una vez que la aplicación finaliza por cualquier motivo, la ubicación temporal se vacía y se quita el archivo. Si la aplicación necesita mantener el acceso al archivo, debe realizar una copia y colocarlo en el contenedor de la aplicación.

El modo de apertura es útil cuando la aplicación desea colaborar con otra aplicación y compartir cualquier cambio realizado en el documento con esa aplicación. El modo de importación se utiliza cuando la aplicación no desea compartir sus modificaciones en un documento con otras aplicaciones.

## <a name="making-a-document-external"></a>Convertir un documento en externo

Como se indicó anteriormente, una aplicación de iOS 8 no tiene acceso a los contenedores fuera de su propio contenedor de aplicaciones. La aplicación puede escribir en su propio contenedor de forma local o en una ubicación temporal y, a continuación, utilizar un modo de documento especial para desplace el documento resultante fuera del contenedor de la aplicación a una ubicación seleccionada por el usuario.

Para trasladar un documento a una ubicación externa, haga lo siguiente:

1. En primer lugar, cree un nuevo documento en una ubicación local o temporal.
1. Cree una `NSUrl` que apunte al nuevo documento.
1. Abra un nuevo controlador de selector de documentos y pásele el `NSUrl` con el modo de `MoveToService`. 
1. Una vez que el usuario elige una nueva ubicación, el documento se mueve de la ubicación actual a la nueva.
1. Se escribirá un documento de referencia en el contenedor de la aplicación de la aplicación para que la aplicación de creación siga accediendo al archivo.

El siguiente código se puede usar para trasladar un documento a una ubicación externa: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

El documento de referencia devuelto por el proceso anterior es exactamente el mismo que el creado por el modo de apertura del selector de documentos. Sin embargo, hay ocasiones en las que la aplicación podría querer trasladar un documento sin mantener una referencia a él.

Para quitar un documento sin generar una referencia, use el modo de `ExportToService`. Ejemplo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Al usar el modo de `ExportToService`, el documento se copia en el contenedor externo y la copia existente se deja en su ubicación original.

## <a name="document-provider-extensions"></a>Extensiones de proveedor de documentos

Con iOS 8, Apple quiere que el usuario final pueda acceder a cualquiera de sus documentos basados en la nube, independientemente de dónde existan realmente. Para lograr este objetivo, iOS 8 proporciona un nuevo mecanismo de extensión del proveedor de documentos.

### <a name="what-is-a-document-provider-extension"></a>¿Qué es una extensión de proveedor de documentos?

Simplemente se ha indicado que una extensión de proveedor de documentos es una forma de que un desarrollador, o de un tercero, proporcione a un almacenamiento de documentos alternativo de aplicación al que se puede tener acceso de la misma manera que la ubicación de almacenamiento de iCloud existente.

El usuario puede seleccionar una de estas ubicaciones de almacenamiento alternativas del selector de documentos y puede usar los mismos modos de acceso (abrir, importar, trasladar o exportar) para trabajar con archivos en esa ubicación.

Esto se implementa mediante dos extensiones diferentes:

- **Extensión del selector de documentos** : proporciona una `UIViewController` subclase que proporciona una interfaz gráfica para que el usuario elija un documento de una ubicación de almacenamiento alternativa. Esta subclase se mostrará como parte del controlador de vistas del selector de documentos.
- **Archivo proporcionar extensión** : se trata de una extensión que no es de interfaz de usuario que se encarga de proporcionar realmente el contenido de los archivos. Estas extensiones se proporcionan a través de la coordinación de archivos (`NSFileCoordinator`). Este es otro caso importante en el que se requiere la coordinación de archivos.

En el diagrama siguiente se muestra el flujo de datos típico al trabajar con extensiones de proveedor de documentos:

 [![](document-picker-images/image39.png "This diagram shows the typical data flow when working with Document Provider Extensions")](document-picker-images/image39.png#lightbox)

Se produce el siguiente proceso:

1. La aplicación presenta un controlador de selector de documentos para permitir al usuario seleccionar un archivo con el que trabajar.
1. El usuario selecciona una ubicación de archivo alternativa y se llama a la extensión de `UIViewController` personalizada para mostrar la interfaz de usuario.
1. El usuario selecciona un archivo de esta ubicación y la dirección URL se devuelve al selector de documentos.
1. El selector de documentos selecciona la dirección URL del archivo y lo devuelve a la aplicación para que el usuario pueda trabajar.
1. La dirección URL se pasa al Coordinador de archivos para devolver el contenido de los archivos a la aplicación.
1. El Coordinador de archivos llama a la extensión de proveedor de archivos personalizada para recuperar el archivo.
1. El contenido del archivo se devuelve al Coordinador de archivos.
1. El contenido del archivo se devuelve a la aplicación.

### <a name="security-and-bookmarks"></a>Seguridad y marcadores

En esta sección se examinará de forma rápida cómo funciona la seguridad y el acceso persistente a archivos a través de marcadores con las extensiones del proveedor de documentos. A diferencia del proveedor de documentos iCloud, que guarda automáticamente la seguridad y los marcadores en el contenedor de la aplicación, las extensiones del proveedor de documentos no se deben a que no forman parte del sistema de referencia del documento.

Por ejemplo: en una configuración de empresa que proporciona su propio almacén de datos seguro para toda la empresa, los administradores no quieren tener acceso a información corporativa confidencial ni procesar los servidores públicos de iCloud. Por lo tanto, no se puede usar el sistema de referencia de documento integrado.

Todavía se puede usar el sistema de marcadores y es responsabilidad de la extensión del proveedor de archivos procesar correctamente una dirección URL marcada y devolver el contenido del documento al que señala.

Por motivos de seguridad, iOS 8 tiene un nivel de aislamiento que conserva la información sobre la aplicación que tiene acceso al identificador dentro del que se encuentra el proveedor de archivos. Se debe observar que este nivel de aislamiento controla todo el acceso a los archivos.

En el diagrama siguiente se muestra el flujo de datos cuando se trabaja con marcadores y una extensión de proveedor de documentos:

 [![](document-picker-images/image40.png "This diagram shows the data flow when working with Bookmarks and a Document Provider Extension")](document-picker-images/image40.png#lightbox)

Se produce el siguiente proceso:

1. La aplicación está a punto de escribir el fondo y debe conservar su estado. Llama a `NSUrl` para crear un marcador en un archivo en un almacenamiento alternativo.
1. `NSUrl` llama a la extensión del proveedor de archivos para obtener una dirección URL persistente para el documento. 
1. La extensión del proveedor de archivos devuelve la dirección URL como una cadena a la `NSUrl`.
1. El `NSUrl` agrupa la dirección URL en un marcador y la devuelve a la aplicación.
1. Cuando la aplicación deja de estar en segundo plano y necesita restaurar el estado, pasa el marcador a `NSUrl`.
1. `NSUrl` llama a la extensión del proveedor de archivos con la dirección URL del archivo.
1. El proveedor de extensión de archivo tiene acceso al archivo y devuelve la ubicación del archivo que se va a `NSUrl`.
1. La ubicación del archivo se incluye con información de seguridad y se devuelve a la aplicación.

Desde aquí, la aplicación puede tener acceso al archivo y trabajar con él de la manera habitual.

### <a name="writing-files"></a>Escribir archivos

En esta sección se examinará de forma rápida cómo funciona la escritura de archivos en una ubicación alternativa con una extensión de proveedor de documentos. La aplicación de iOS usará la coordinación de archivos para guardar información en el disco dentro del contenedor de la aplicación. Poco después de que el archivo se haya escrito correctamente, se notificará el cambio a la extensión del proveedor de archivos.

En este momento, la extensión del proveedor de archivos puede empezar a cargar el archivo en la ubicación alternativa (o marcar el archivo como sucio y requerir carga).

### <a name="creating-new-document-provider-extensions"></a>Crear nuevas extensiones de proveedor de documentos

La creación de nuevas extensiones de proveedor de documentos está fuera del ámbito de este artículo introductorio. Esta información se proporciona aquí para mostrar que, en función de las extensiones que un usuario ha cargado en su dispositivo iOS, una aplicación puede tener acceso a ubicaciones de almacenamiento de documentos fuera de la ubicación de iCloud proporcionada por Apple.

El desarrollador debe ser consciente de este hecho al usar el selector de documentos y trabajar con documentos externos. No deben suponer que los documentos se hospedan en iCloud.

Para obtener más información sobre la creación de una extensión de proveedor de almacenamiento o de selector de documentos, consulte el documento [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md) .

## <a name="migrating-to-icloud-drive"></a>Migrar a la unidad iCloud

En iOS 8, los usuarios pueden optar por seguir usando el sistema de documentos de iCloud existente usado en iOS 7 (y sistemas anteriores) o pueden optar por migrar los documentos existentes al nuevo mecanismo de unidad de iCloud.

En Mac OS X Yosemite, Apple no proporciona la compatibilidad con versiones anteriores, por lo que todos los documentos se deben migrar a la unidad iCloud o ya no se actualizarán entre los dispositivos.

Después de migrar la cuenta de un usuario a la unidad de iCloud, solo los dispositivos que usan la unidad de iCloud podrán propagar los cambios a los documentos en dichos dispositivos.

> [!IMPORTANT]
> Los desarrolladores deben tener en cuenta que las nuevas características que se describen en este artículo solo están disponibles si la cuenta del usuario se ha migrado a la unidad iCloud. 

## <a name="summary"></a>Resumen

En este artículo se han tratado los cambios en las API de iCloud existentes necesarias para admitir la unidad de iCloud y el controlador de vista del nuevo selector de documentos. Ha tratado la coordinación de archivos y por qué es importante cuando se trabaja con documentos basados en la nube. Ha tratado la configuración necesaria para habilitar documentos basados en la nube en una aplicación de Xamarin. iOS y ha dado una visión introductoria del trabajo con documentos fuera del contenedor de la aplicación de una aplicación mediante el controlador de vista del selector de documentos.

Además, en este artículo se han tratado brevemente las extensiones de proveedor de documentos y por qué el desarrollador debe ser consciente de ellas al escribir aplicaciones que puedan controlar documentos basados en la nube.

## <a name="related-links"></a>Vínculos relacionados

- [DocPicker (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-docpicker)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introducción a las extensiones de aplicaciones](~/ios/platform/extensions.md)
