---
title: CloudKit en Xamarin. iOS
description: En este documento se describe cómo trabajar con CloudKit en Xamarin. iOS. Proporciona una visión general de CloudKit y explica cómo habilitarlo, la API de comodidad CloudKit, la escalabilidad, las cuentas de usuario y los entornos de desarrollo y producción.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: 29e737e5a6cb6abdae099c0224a2da058c2ea025
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527735"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit en Xamarin. iOS

El marco CloudKit agiliza el desarrollo de aplicaciones que tienen acceso a iCloud. Esto incluye la recuperación de los datos de la aplicación y los derechos de los recursos, así como la posibilidad de almacenar información de la aplicación de forma segura. Este kit proporciona a los usuarios una capa de anonimato al permitir el acceso a las aplicaciones con sus identificadores de iCloud sin compartir información personal.

Los desarrolladores pueden centrarse en las aplicaciones del lado cliente y dejar que iCloud elimine la necesidad de escribir lógica de aplicación del lado servidor. CloudKit proporciona bases de datos públicas, privadas y de autenticación, así como servicios de almacenamiento de datos y datos estructurados.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="requirements"></a>Requisitos

Para completar los pasos que se describen en este artículo, es necesario lo siguiente:

- **Xcode y el SDK de iOS** : las API Xcode y iOS 8 de Apple deben instalarse y configurarse en el equipo del desarrollador.
- **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo del usuario.
- **dispositivo iOS 8** : dispositivo iOS que ejecuta la versión más reciente de iOS 8 para realizar pruebas.

## <a name="what-is-cloudkit"></a>¿Qué es CloudKit?

CloudKit es una manera de conceder a los desarrolladores acceso a los servidores de iCloud. Proporciona la base para la unidad de iCloud y la biblioteca de fotografías de iCloud. CloudKit se admite en dispositivos de Mac OS X y Apple iOS.

 [![](intro-to-cloudkit-images/image1.png "Cómo se admite CloudKit en dispositivos de Mac OS X y Apple iOS")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit usa la infraestructura de la cuenta de iCloud. Si hay un usuario que ha iniciado sesión en una cuenta de iCloud en el dispositivo, CloudKit usará su identificador para identificar al usuario. Si no hay ninguna cuenta disponible, se proporcionará acceso limitado de solo lectura.

CloudKit admite el concepto de bases de datos públicas y privadas. Las bases de datos públicas proporcionan una "sopa" de todos los datos a los que el usuario tiene acceso. Las bases de datos privadas están diseñadas para almacenar los datos privados enlazados a un usuario específico.

CloudKit admite datos estructurados y en bloque. Es capaz de administrar las transferencias de archivos de gran tamaño sin problemas. CloudKit se encarga de transferir eficazmente archivos grandes a y desde los servidores de iCloud en segundo plano, lo que libera al desarrollador para que se Centre en otras tareas.

> [!NOTE]
> Es importante tener en cuenta que CloudKit es una _tecnología de transporte_. No proporciona ninguna persistencia; solo permite a una aplicación enviar y recibir información de los servidores de forma eficaz.

En el momento de redactar este documento, Apple proporciona inicialmente CloudKit de forma gratuita con un límite alto en cuanto al ancho de banda y la capacidad de almacenamiento. En el caso de aplicaciones o proyectos más grandes con una base de usuarios grande, Apple ha sugerido que se proporcionará una escala de precios asequible.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Habilitación de CloudKit en una aplicación de Xamarin

Para que una aplicación de Xamarin pueda usar el marco de trabajo de CloudKit, la aplicación se debe aprovisionar correctamente como se detalla en las guías [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) y [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) .

1. Abra el proyecto en Visual Studio para Mac o Visual Studio.
2. En el **Explorador de soluciones**, abra el archivo **info. plist** y asegúrese de que el **identificador** de la agrupación coincide con el que se definió en **ID. de aplicación** creado como parte de la configuración de aprovisionamiento:
 
    [![](intro-to-cloudkit-images/image26a.png "Escriba el identificador de paquete")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3. Desplácese hacia abajo hasta la parte inferior del archivo **info. plist** y seleccione **modos en segundo plano habilitados**, **actualizaciones de ubicación** y notificaciones **remotas**:

    [![](intro-to-cloudkit-images/image27a.png "Seleccionar modos en segundo plano habilitados, actualizaciones de ubicación y notificaciones remotas")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4. Haga clic con el botón derecho en el proyecto de iOS en la solución y seleccione **Opciones**.
5. Seleccione **firma de lote de iOS**, seleccione la **identidad del desarrollador** y el **Perfil de aprovisionamiento** que creó anteriormente.
6. Asegúrese de que el archivo contitles **. plist** incluye **Habilitar iCloud** , **almacenamiento de clave-valor** y **CloudKit** .
7. Asegúrese de que el **contenedor ubicuidad** existe para la aplicación (como se creó anteriormente). Ejemplo: `iCloud.com.your-company.CloudKitAtlas`
8. Guarde los cambios en el archivo.


Con esta configuración en contexto, la aplicación ya está lista para acceder a las API de CloudKit Framework.

## <a name="cloudkit-api-overview"></a>Información general sobre la API de CloudKit

Antes de implementar CloudKit en una aplicación de Xamarin iOS, este artículo tratará los aspectos básicos del marco de CloudKit, que incluirá los temas siguientes:

1. **Contenedores** : silos aislados de comunicaciones de iCloud.
2. **Bases de datos** : públicas y privadas están disponibles para la aplicación.
3. **Registros** : mecanismo en el que los datos estructurados se mueven hacia y desde CloudKit.
4. **Zonas de registro** : son grupos de registros.
5. Los identificadores de **registro** : están totalmente normalizados y representan la ubicación específica del registro.
6. **Referencia** : proporcione relaciones de elementos primarios y secundarios entre los registros relacionados de una base de datos determinada.
7. **Activos** : permite cargar archivos de datos grandes y no estructurados en iCloud y asociarlos a un registro determinado.


### <a name="containers"></a>Contenedores

Una aplicación determinada que se ejecuta en un dispositivo iOS siempre se ejecuta junto con otras aplicaciones y servicios de ese dispositivo. En el dispositivo cliente, la aplicación se va a silor o estar en un espacio aislado de alguna manera. En algunos casos, se trata de un espacio aislado literal y, en otros, la aplicación se está ejecutando simplemente en su propio espacio de memoria.

El concepto de tomar una aplicación cliente y ejecutarla separada de otros clientes es muy eficaz y ofrece las siguientes ventajas:

1. **Seguridad** : una aplicación no puede interferir con otras aplicaciones cliente ni con el propio sistema operativo.
1. **Estabilidad** : Si la aplicación cliente se bloquea, no puede usar otras aplicaciones del sistema operativo.
1. **Privacidad** : cada aplicación cliente tiene acceso limitado a la información personal almacenada en el dispositivo.


CloudKit se diseñó para ofrecer las mismas ventajas que las mencionadas anteriormente y aplicarlos al trabajar con información basada en la nube:

 [![](intro-to-cloudkit-images/image31.png "Las aplicaciones CloudKit se comunican mediante contenedores")](intro-to-cloudkit-images/image31.png#lightbox)

Del mismo modo que la aplicación es una de las muchas que se ejecutan en el dispositivo, es decir, las comunicaciones de la aplicación con iCloud, una de las otras. Cada uno de estos silos de comunicación diferentes se denominan contenedores.

Los contenedores se exponen en el marco `CKContainer` de CloudKit a través de la clase. De forma predeterminada, una aplicación se comunica con un contenedor y este contenedor separa los datos de esa aplicación. Esto significa que varias aplicaciones pueden almacenar información en la misma cuenta de iCloud, pero esta información nunca se entremezclará.

La inclusión de los datos de iCloud también permite a CloudKit encapsular información del usuario. De esta manera, la aplicación tendrá un acceso limitado a la cuenta de iCloud y a la información de usuario almacenada en, todo ello sin dejar de proteger la privacidad y la seguridad del usuario.

Los contenedores están totalmente administrados por el desarrollador de la aplicación a través del portal de WWDR. El espacio de nombres del contenedor es global en todos los desarrolladores de Apple, por lo que el contenedor no solo debe ser único para las aplicaciones de un desarrollador determinado, sino también para todos los desarrolladores y aplicaciones de Apple.

Apple sugiere el uso de la notación DNS inversa al crear el espacio de nombres para los contenedores de la aplicación. Ejemplo:

```csharp
iCloud.com.company-name.application-name
```

Aunque los contenedores están enlazados de uno en uno a una aplicación determinada de forma predeterminada, se pueden compartir entre las aplicaciones. Por tanto, varias aplicaciones pueden coordinarse en un solo contenedor. Una sola aplicación también puede comunicarse con varios contenedores.

### <a name="databases"></a>Bases de datos

Una de las funciones principales de CloudKit es tomar el modelo de datos y la replicación de una aplicación que se modelan en los servidores de iCloud. Parte de la información está pensada para el usuario que la creó, otra información es datos públicos que un usuario podría crear para uso público (por ejemplo, una revisión de un restaurante) o podría ser información que el desarrollador ha publicado para la aplicación. En cualquier caso, la audiencia no es solo un usuario, sino que es una comunidad de personas.

 [![](intro-to-cloudkit-images/image32.png "Diagrama de contenedor de CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

Dentro de un contenedor, primero y más importante es la base de datos pública. Aquí es donde toda la información pública vive y se entremezcla. Además, hay varias bases de datos privadas individuales para cada usuario de la aplicación.

Cuando se ejecuta en un dispositivo iOS, la aplicación solo tendrá acceso a la información del usuario de iCloud actualmente conectado. Por lo tanto, la vista de la aplicación del contenedor será la siguiente:

 [![](intro-to-cloudkit-images/image33.png "Vista de aplicaciones del contenedor")](intro-to-cloudkit-images/image33.png#lightbox)

Solo puede ver la base de datos pública y la base de datos privada asociada al usuario de iCloud que ha iniciado sesión actualmente.

Las bases de datos se exponen en el marco `CKDatabase` de CloudKit a través de la clase. Cada aplicación tiene acceso a dos bases de datos: la base de datos pública y la privada.

El contenedor es el punto de entrada inicial en CloudKit. El siguiente código se puede usar para tener acceso a la base de datos pública y privada desde el contenedor predeterminado de la aplicación:

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

Estas son las diferencias entre los tipos de base de datos:

||Base de datos pública|Base de datos privada|
|---|--- |--- |
|**Tipo de datos**|Datos compartidos|Datos del usuario actual|
|**Límite**|Se tiene en cuenta en la cuota del desarrollador|Se tiene en cuenta en la cuota del usuario|
|**Permisos predeterminados**|Mundo legible|Usuario legible|
|**Permisos de edición**|Roles del panel de iCloud a través de un nivel de clase de registro|N/D|

### <a name="records"></a>Registros

Los contenedores contienen bases de datos y, dentro de las bases de datos, son registros. Los registros son el mecanismo en el que los datos estructurados se mueven hacia y desde CloudKit:

 [![](intro-to-cloudkit-images/image34.png "Los contenedores contienen bases de datos y, dentro de las bases de datos, son registros")](intro-to-cloudkit-images/image34.png#lightbox)

Los registros se exponen en el marco `CKRecord` de CloudKit a través de la clase, que contiene pares clave-valor. Una instancia de un objeto en una aplicación es equivalente a `CKRecord` en CloudKit. Además, cada uno `CKRecord` posee un tipo de registro, que es equivalente a la clase de un objeto.

Los registros tienen un esquema Just-in-Time, por lo que los datos se describen en CloudKit antes de que se entreguen para su procesamiento. A partir de ese momento, CloudKit interpretará la información y administrará la logística de almacenar y recuperar el registro.

La `CKRecord` clase también admite una amplia gama de metadatos. Por ejemplo, un registro contiene información sobre cuándo se creó y el usuario que lo creó. Un registro también contiene información sobre cuándo se modificó por última vez y el usuario que lo modificó.

Los registros contienen la noción de una etiqueta de cambio. Se trata de una versión anterior de una revisión de un registro determinado. La etiqueta de cambio se utiliza como una forma ligera de determinar si el cliente y el servidor tienen la misma versión de un registro determinado.

Como se indicó anteriormente `CKRecords` , ajuste los pares clave-valor y, como tal, se pueden almacenar los siguientes tipos de datos en un registro:

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`


Además de los tipos de valor único, un registro puede contener una matriz homogénea de cualquiera de los tipos enumerados anteriormente.

El siguiente código se puede usar para crear un nuevo registro y almacenarlo en una base de datos:

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Zonas de registro

Los registros no existen en una base de datos determinada: los grupos de registros existen juntos dentro de una zona de registro. Las zonas de registro se pueden considerar como tablas en una base de datos relacional tradicional:

 [![](intro-to-cloudkit-images/image35.png "Los grupos de registros existen juntos dentro de una zona de registro")](intro-to-cloudkit-images/image35.png#lightbox)

Puede haber varios registros dentro de una zona de registro determinada y varias zonas de registro dentro de una base de datos determinada. Cada base de datos contiene una zona de registro predeterminada:

 [![](intro-to-cloudkit-images/image36.png "Cada base de datos contiene una zona de registro predeterminada y una zona personalizada")](intro-to-cloudkit-images/image36.png#lightbox)

Aquí es donde se almacenan los registros de forma predeterminada. Además, se pueden crear zonas de registro personalizadas. Las zonas de registro representan la granularidad base en la que se realizan las confirmaciones atómicas y Change Tracking.

## <a name="record-identifiers"></a>Identificadores de registro

Los identificadores de registro se representan como una tupla que contiene un nombre de registro proporcionado por el cliente y la zona en la que existe el registro. Los identificadores de registro tienen las siguientes características:

- Los crea la aplicación cliente.
- Están totalmente normalizadas y representan la ubicación específica del registro.
- Mediante la asignación del identificador único de un registro en una base de datos externa al nombre del registro, se pueden usar para crear un puente entre las bases de datos locales que no se almacenan en CloudKit.


Cuando los desarrolladores crean nuevos registros, pueden optar por pasar un identificador de registro. Si no se especifica un identificador de registro, se creará automáticamente un UUID y se asignará al registro.

Cuando los desarrolladores crean nuevos identificadores de registro, pueden elegir especificar la zona de registro a la que pertenecerá cada registro. Si no se especifica ninguno, se utilizará la zona de registro predeterminada.

Los identificadores de registro se exponen en el `CKRecordID` marco de CloudKit a través de la clase. El siguiente código se puede usar para crear un nuevo identificador de registro:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Referencias

Las referencias proporcionan relaciones entre los registros relacionados en una base de datos determinada:

 [![](intro-to-cloudkit-images/image37.png "Las referencias proporcionan relaciones entre los registros relacionados en una base de datos determinada")](intro-to-cloudkit-images/image37.png#lightbox)

En el ejemplo anterior, los elementos primarios propios son secundarios para que el elemento secundario sea un registro secundario del Registro primario. La relación va del registro secundario al Registro primario y se conoce como *referencia inversa*.

Las referencias se exponen en el marco `CKReference` de CloudKit a través de la clase. Son una manera de permitir que el servidor de iCloud comprenda la relación entre los registros.

Las referencias proporcionan el mecanismo detrás de las eliminaciones en cascada. Si se elimina un registro primario de la base de datos, los registros secundarios (como se especifican en una relación) también se eliminarán automáticamente de la base de datos.

> [!NOTE]
> Los punteros pendientes son una posibilidad cuando se usa CloudKit. Por ejemplo, en el momento en que la aplicación captura una lista de punteros de registro, selecciona un registro y, a continuación, solicita el registro, es posible que el registro ya no exista en la base de datos. Una aplicación debe estar codificada para controlar esta situación correctamente.

Aunque no es necesario, se prefieren las referencias inversas al trabajar con el marco de trabajo de CloudKit. Apple ha optimizado el sistema para que sea el tipo de referencia más eficaz.

Al crear una referencia, el desarrollador puede proporcionar un registro que ya esté en la memoria o crear una referencia a un identificador de registro. Si se usa un identificador de registro y la referencia especificada no existía en la base de datos, se crearía un puntero pendiente.

El siguiente es un ejemplo de cómo crear una referencia en un registro conocido:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Activos

Los recursos permiten cargar un archivo de datos grandes y no estructurados en iCloud y asociarlo a un registro determinado:

 [![](intro-to-cloudkit-images/image38.png "Los recursos permiten cargar un archivo de datos grandes y no estructurados en iCloud y asociarlo a un registro determinado.")](intro-to-cloudkit-images/image38.png#lightbox)

En el cliente, se `CKRecord` crea un que describe el archivo que se va a cargar en el servidor de iCloud. Se `CKAsset` crea un que contiene el archivo y está vinculado al registro que lo describe.

Cuando el archivo se carga en el servidor, el registro se coloca en la base de datos y el archivo se copia en una base de datos de almacenamiento masivo especial. Se crea un vínculo entre el puntero de registro y el archivo cargado.

Los recursos se exponen en el marco `CKAsset` de trabajo de CloudKit a través de la clase y se usan para almacenar datos grandes y no estructurados. Dado que el desarrollador nunca desea tener grandes datos no estructurados en la memoria, los recursos se implementan mediante archivos en disco.

Los recursos son propiedad de los registros, lo que permite recuperar los recursos de iCloud mediante el registro como un puntero. De este modo, el servidor puede recopilar elementos no utilizados cuando se elimina el registro que posee el recurso.

Dado `CKAssets` que están pensados para administrar archivos de datos de gran tamaño, Apple diseñó CloudKit para cargar y descargar eficazmente los recursos.

El siguiente código se puede usar para crear un recurso y asociarlo al registro:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Ahora hemos tratado todos los objetos fundamentales dentro de CloudKit. Los contenedores están asociados a aplicaciones y contienen bases de datos. Las bases de datos contienen registros agrupados en zonas de registro y a los que apuntan los identificadores de registro. Las relaciones de elementos primarios y secundarios se definen entre los registros mediante referencias. Por último, los archivos grandes se pueden cargar y asociar a registros mediante recursos.

## <a name="cloudkit-convenience-api"></a>API de comodidad de CloudKit

Apple ofrece dos conjuntos de API diferentes para trabajar con CloudKit:

- **API operativa** : ofrece todas las características únicas de CloudKit. En el caso de aplicaciones más complejas, esta API proporciona un control exhaustivo sobre CloudKit.
- **Comodidad API** : ofrece un subconjunto común preconfigurado de características de CloudKit. Proporciona una solución cómoda y sencilla de acceso para incluir la funcionalidad de CloudKit en una aplicación de iOS.


La comodidad de la API suele ser la mejor opción para la mayoría de las aplicaciones de iOS y Apple sugiere comenzar con ella. En el resto de esta sección se tratarán los siguientes temas útiles de la API:

- Guardar un registro.
- Capturando un registro.
- Actualizar un registro.


### <a name="common-setup-code"></a>Código de instalación común

Antes de empezar a usar la API de CloudKit Practice, se requiere un código de configuración estándar. Empiece por modificar el archivo de `AppDelegate.cs` la aplicación y haga que tenga un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

El código anterior expone las bases de datos CloudKit públicas y privadas como métodos abreviados para facilitar el trabajo en el resto de la aplicación.

A continuación, agregue el código siguiente a cualquier contenedor de vistas o vistas que vaya a usar CloudKit:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Esto agrega un acceso directo para obtener acceso `AppDelegate` a y acceder a los accesos directos de base de datos públicos y privados creados anteriormente.

Con este código en su lugar, echemos un vistazo a la implementación de CloudKit Practice API en una aplicación Xamarin iOS 8.

### <a name="saving-a-record"></a>Guardar un registro

Con el patrón que se presentó anteriormente al hablar de registros, el código siguiente creará un nuevo registro y usará la API de conveniencia para guardarlo en la base de datos pública:

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Tres aspectos que hay que tener en cuenta sobre el código anterior:

1. Al llamar al `SaveRecord` método `PublicDatabase`de, el desarrollador no tiene que especificar cómo se envían los datos, en qué zona se está escribiendo, etc. La comodidad de la API se ocupa de todos los detalles.
1. La llamada es asincrónica y proporciona una rutina de devolución de llamada cuando se completa la llamada, bien con éxito o con errores. Si se produce un error en la llamada, se proporcionará un mensaje de error.
1. CloudKit no proporciona almacenamiento ni persistencia local. es solo un medio de transferencia. Por tanto, cuando se realiza una solicitud para guardar un registro, se envía inmediatamente a los servidores de iCloud.


> [!NOTE]
> Debido a la naturaleza "perdida" de las comunicaciones de red móvil, en las que las conexiones se pierden o se interrumpen constantemente, una de las primeras consideraciones que debe tomar el desarrollador al trabajar con CloudKit es el control de errores.

### <a name="fetching-a-record"></a>Captura de un registro

Con un registro creado y almacenado correctamente en el servidor de iCloud, use el siguiente código para recuperar el registro:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Al igual que al guardar el registro, el código anterior es asincrónico, simple y requiere un control de errores excelente.

### <a name="updating-a-record"></a>Actualización de un registro

Una vez que se ha capturado un registro de los servidores de iCloud, se puede usar el código siguiente para modificar el registro y guardar los cambios de nuevo en la base de datos:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

El `FetchRecord` método `PublicDatabase` de devuelve`CKRecord` si la llamada se realizó correctamente. A continuación, la aplicación modifica el registro y `SaveRecord` llama de nuevo para volver a escribir los cambios en la base de datos.

En esta sección se ha mostrado el ciclo típico que utilizará una aplicación al trabajar con la API de CloudKit Practice. La aplicación guardará los registros en iCloud, recuperará esos registros de iCloud, modificará los registros y guardará los cambios de nuevo en iCloud.

## <a name="designing-for-scalability"></a>Diseño para la escalabilidad

Hasta ahora este artículo ha examinado el almacenamiento y la recuperación del modelo de objetos completo de una aplicación desde los servidores de iCloud, cada vez que se va a trabajar con él. Aunque este enfoque funciona bien con una pequeña cantidad de datos y una base de usuarios muy pequeña, no se escala bien cuando aumenta la cantidad de información o de base de usuarios.

### <a name="big-data-tiny-device"></a>Big Data, pequeño dispositivo

Cuanto más popular es la aplicación, más datos en la base de datos y lo menos factible es tener una memoria caché de todos los datos del dispositivo. Se pueden utilizar las técnicas siguientes para solucionar este problema:

- **Conserve los datos de gran tamaño en la nube** : CloudKit se diseñó para tratar datos de gran tamaño de manera eficaz.
- El **cliente solo debe ver un segmento de los datos** : Desactive el mínimo de datos necesarios para controlar cualquier tarea en un momento dado.
- Las **vistas de cliente pueden cambiar** : dado que cada usuario tiene preferencias diferentes, el segmento de datos que se muestra puede cambiar de un usuario a otro, y la vista individual del usuario de cualquier segmento determinado puede ser diferente.
- El **cliente utiliza consultas para centrarse en el punto de vista** : las consultas permiten al usuario ver un pequeño subconjunto de un conjunto de información mayor que existe dentro de la nube.


### <a name="queries"></a>Consultas

Como se indicó anteriormente, las consultas permiten al desarrollador seleccionar un pequeño subconjunto del conjunto de DataSet más grande que existe en la nube. Las consultas se exponen en el marco `CKQuery` de CloudKit a través de la clase.

Una consulta combina tres cosas diferentes: un tipo de registro `RecordType`(), un predicado ( `NSPredicate`) y, opcionalmente, un descriptor de ordenación ( `NSSortDescriptors`). CloudKit es compatible con `NSPredicate`la mayoría de.

#### <a name="supported-predicates"></a>Predicados admitidos

CloudKit admite los siguientes tipos de `NSPredicates` cuando se trabaja con consultas:


1. Registros coincidentes en los que el nombre es igual a un valor almacenado en una variable:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Permite que la búsqueda de coincidencias se base en un valor de clave dinámica, de modo que la clave no tenga que conocerse en tiempo de compilación:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Registros coincidentes en los que el valor del registro es mayor que el valor especificado:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Registros coincidentes en los que la ubicación del registro está dentro de 100 metros de la ubicación especificada:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit admite una búsqueda con tokens. Esta llamada creará dos tokens, uno para `after` y otro para `session`. Devolverá un registro que contiene los dos tokens:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
6. CloudKit admite predicados compuestos Unidos mediante el `AND` operador.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Crear consultas

El siguiente código se puede usar para crear un `CKQuery` en una aplicación Xamarin iOS 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

En primer lugar, crea un predicado para seleccionar solo los registros que coinciden con un nombre determinado. A continuación, crea una consulta que seleccionará los registros del tipo de registro especificado que coinciden con el predicado.

#### <a name="performing-a-query"></a>Realización de una consulta

Una vez creada una consulta, use el código siguiente para realizar la consulta y procesar los registros devueltos:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

El código anterior toma la consulta creada anteriormente y la ejecuta en la base de datos pública. Dado que no se especifica ninguna zona de registro, se busca en todas las zonas. Si no se ha producido ningún error, `CKRecords` se devolverá una matriz de que coincida con los parámetros de la consulta.

La manera de pensar en las consultas es que son los sondeos y son excelentes en la segmentación a través de grandes conjuntos de valores. Sin embargo, las consultas no son adecuadas para conjuntos de DataSet grandes y principalmente estáticos debido a los siguientes motivos:

- No son válidas para la duración de la batería del dispositivo.
- No son válidos para el tráfico de red.
- Son incorrectas para la experiencia del usuario porque la información que ven está limitada por la frecuencia con la que la aplicación sondea la base de datos. Los usuarios hoy en día esperan las notificaciones de envío cuando se produce algún cambio.


### <a name="subscriptions"></a>Suscripciones

Cuando se trabaja con conjuntos de DataSet grandes y principalmente estáticos, la consulta no se debe realizar en el dispositivo cliente, sino que debe ejecutarse en el servidor en nombre del cliente. La consulta debe ejecutarse en segundo plano y debe ejecutarse después de guardar cada registro único, ya sea por el dispositivo actual o por otro dispositivo que toque la misma base de datos.

Por último, se debe enviar una notificación de envío a todos los dispositivos conectados a la base de datos cuando se ejecuta la consulta del lado servidor.

Las suscripciones se exponen en el marco `CKSubscription` de CloudKit a través de la clase. Combinan un tipo de registro ( `RecordType`), un predicado `NSPredicate`() y una notificación de extracción `Push`de Apple ().

> [!NOTE]
> Las inserciones de CloudKit se aumentan ligeramente, ya que contienen una carga útil que contiene información específica de CloudKit, como la causa de que se produjera la inserciones.

#### <a name="how-subscriptions-work"></a>Cómo funcionan las suscripciones

Antes de implementar la C# suscripción en el código, echemos un vistazo rápido a cómo funcionan las suscripciones:

 [![](intro-to-cloudkit-images/image39.png "Información general sobre el funcionamiento de las suscripciones")](intro-to-cloudkit-images/image39.png#lightbox)

En el gráfico anterior se muestra el proceso de suscripción típico de la siguiente manera:

1. El dispositivo cliente crea una nueva suscripción que contiene el conjunto de condiciones que desencadenará la suscripción y una notificación de extracción que se enviará cuando se produzca el desencadenador.
2. La suscripción se envía a la base de datos donde se agrega a la colección de suscripciones existentes.
3. Un segundo dispositivo crea un nuevo registro y guarda dicho registro en la base de datos.
4. La base de datos busca en su lista de suscripciones para ver si el nuevo registro coincide con cualquiera de sus condiciones.
5. Si se encuentra una coincidencia, la notificación de extracción se envía al dispositivo que registró la suscripción con información sobre el registro que hizo que se desencadenara.


Con este conocimiento implementado, echemos un vistazo a la creación de suscripciones en una aplicación Xamarin iOS 8.

#### <a name="creating-subscriptions"></a>Crear suscripciones

El siguiente código se puede utilizar para crear una suscripción:

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

En primer lugar, crea un predicado que proporciona la condición para desencadenar la suscripción. A continuación, se crea la suscripción con un tipo de registro específico y se establece la opción de cuando se prueba el desencadenador. Por último, define el tipo de notificación que se producirá cuando se desencadene la suscripción y la adjunte a la suscripción.

### <a name="saving-subscriptions"></a>Guardar suscripciones

Con la suscripción creada, el código siguiente lo guardará en la base de datos:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Con la comodidad de la API, la llamada es asincrónica, simple y proporciona un control de errores sencillo.

#### <a name="handling-push-notifications"></a>Control de notificaciones de envío

Si el desarrollador ha usado previamente notificaciones de extracción de Apple (APS), el proceso de tratar con las notificaciones generadas por CloudKit debe ser familiar.

En, invalide `ReceivedRemoteNotification` la clase del modo siguiente: `AppDelegate.cs`

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

El código anterior pide a CloudKit que analice la userInfo en una notificación CloudKit. A continuación, se extrae información acerca de la alerta. Por último, se prueba el tipo de notificación y la notificación se administra en consecuencia.

En esta sección se ha mostrado cómo responder a los macrodatos, el pequeño problema del dispositivo presentado anteriormente mediante consultas y suscripciones. La aplicación dejará sus datos de gran tamaño en la nube y utilizará estas tecnologías para proporcionar vistas en este conjunto de datos.

## <a name="cloudkit-user-accounts"></a>Cuentas de usuario de CloudKit

Como se indicó al principio de este artículo, CloudKit se basa en la infraestructura existente de iCloud. En la siguiente sección se tratan, en detalle, cómo se exponen las cuentas a un desarrollador mediante la API de CloudKit.

### <a name="authentication"></a>Authentication

Al trabajar con cuentas de usuario, la primera consideración es la autenticación. CloudKit admite la autenticación mediante el usuario que ha iniciado sesión actualmente en el dispositivo. La autenticación se realiza en segundo plano y se controla mediante iOS. De esta manera, los desarrolladores nunca tienen que preocuparse de los detalles de la implementación de la autenticación. Solo prueban para ver si un usuario ha iniciado sesión.

### <a name="user-account-information"></a>Información de cuenta de usuario

CloudKit proporciona la siguiente información de usuario al desarrollador:

- **Identity** : una manera de identificar al usuario de forma única.
- **Metadatos** : la capacidad de guardar y recuperar información acerca de los usuarios.
- **Privacidad** : toda la información se trata en un puntual de privacidad consciente. No se expone nada a menos que el usuario lo haya aceptado.
- **Detección** : ofrece a los usuarios la capacidad de detectar a sus amigos que usan la misma aplicación.


A continuación, veremos estos temas en detalle.

#### <a name="identity"></a>identidad

Como se indicó anteriormente, CloudKit proporciona una manera para que la aplicación identifique de forma única a un usuario determinado:

 [![](intro-to-cloudkit-images/image40.png "Identificación de forma exclusiva a un usuario determinado")](intro-to-cloudkit-images/image40.png#lightbox)

Hay una aplicación cliente que se ejecuta en los dispositivos de un usuario y todas las bases de datos privadas del usuario específicas dentro del contenedor CloudKit. La aplicación cliente se va a vincular a uno de esos usuarios específicos. Esto se basa en el usuario que ha iniciado sesión en iCloud localmente en el dispositivo.

Dado que procede de iCloud, hay una gran cantidad de datos de usuario. Y como iCloud hospeda realmente el contenedor, puede correlacionar a los usuarios. En el gráfico anterior, el usuario cuya cuenta `user@icloud.com` de icloud está vinculada al cliente actual.

En un contenedor por contenedor, se crea un identificador de usuario único y generado aleatoriamente y se asocia a la cuenta de iCloud del usuario (dirección de correo electrónico). Este identificador de usuario se devuelve a la aplicación y se puede usar de cualquier manera que el desarrollador considere adecuado.

> [!NOTE]
> Las distintas aplicaciones que se ejecutan en el mismo dispositivo para el mismo usuario de iCloud tendrán distintos identificadores de usuario, ya que están conectados a distintos contenedores de CloudKit.

El código siguiente obtiene el identificador de usuario de CloudKit para el usuario que ha iniciado sesión actualmente en iCloud en el dispositivo:

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

En el código anterior se pide al contenedor CloudKit que proporcione el identificador del usuario que ha iniciado sesión actualmente. Puesto que esta información procede del servidor iCloud, la llamada es asincrónica y se requiere control de errores.

#### <a name="metadata"></a>Metadatos

Cada usuario de CloudKit tiene metadatos específicos que los describen. Estos metadatos se representan como un registro CloudKit:

 [![](intro-to-cloudkit-images/image41.png "Cada usuario de CloudKit tiene metadatos específicos que los describen")](intro-to-cloudkit-images/image41.png#lightbox)

Al buscar en la base de datos privada un usuario específico de un contenedor, hay un registro que define a ese usuario. Hay muchos registros de usuario en la base de datos pública, uno para cada usuario del contenedor. Uno de ellos tendrá un identificador de registro que coincide con el identificador de registro del usuario que ha iniciado sesión actualmente.

Los registros de usuario de la base de datos pública son legibles en todo el mundo. Se tratan, en su mayor parte, como un registro ordinario y tienen un tipo de `CKRecordTypeUserRecord`. Estos registros están reservados por el sistema y no están disponibles para las consultas.

Use el código siguiente para tener acceso a un registro de usuario:

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

En el código anterior se solicita a la base de datos pública que devuelva el registro de usuario para el usuario cuyo identificador se ha tenido acceso anteriormente. Puesto que esta información procede del servidor iCloud, la llamada es asincrónica y se requiere control de errores.

#### <a name="privacy"></a>Privacidad

De forma predeterminada, CloudKit estaba diseñada para proteger la privacidad del usuario que ha iniciado sesión actualmente. De forma predeterminada, no se expone ninguna información de identificación personal sobre el usuario. Hay algunos casos en los que la aplicación necesitará información limitada sobre el usuario.

En estos casos, la aplicación puede solicitar que el usuario divulgue esta información. Se presentará un cuadro de diálogo al usuario pidiéndole que participe para exponer la información de su cuenta.

#### <a name="discovery"></a>Detección

Suponiendo que el usuario ha optado por permitir el acceso limitado a la aplicación a la información de su cuenta de usuario, puede ser reconocible para otros usuarios de la aplicación:

 [![](intro-to-cloudkit-images/image42.png "Un usuario puede ser reconocible para otros usuarios de la aplicación")](intro-to-cloudkit-images/image42.png#lightbox)

La aplicación cliente se está comunicando con un contenedor y el contenedor habla a iCloud para acceder a la información del usuario. El usuario puede proporcionar una dirección de correo electrónico y la detección se puede usar para volver a obtener información sobre el usuario. Opcionalmente, el ID. de usuario también se puede usar para detectar información sobre el usuario.

CloudKit también proporciona una manera de detectar información acerca de cualquier usuario que pueda ser amigo del usuario que ha iniciado sesión actualmente en iCloud consultando toda la libreta de direcciones. El proceso de CloudKit extraerá el libro de contactos del usuario y usará las direcciones de correo electrónico para ver si puede encontrar otros usuarios de la aplicación que coincidan con esas direcciones.

Esto permite que la aplicación aproveche el libro de contactos del usuario sin proporcionar acceso a él ni pedir al usuario que apruebe el acceso a los contactos. En ningún momento, la información de contacto se pone a disposición de la aplicación, solo el proceso de CloudKit tiene acceso.

En Resumen, hay tres tipos diferentes de entradas disponibles para la detección de usuarios:

- **Identificador de registro de usuario** : la detección se puede realizar con el identificador de usuario del usuario que ha iniciado sesión en CloudKit.
- **Dirección de correo electrónico del usuario** : el usuario puede proporcionar una dirección de correo electrónico y se puede usar para la detección.
- **Libro de contacto** : la libreta de direcciones del usuario se puede usar para detectar a los usuarios de la aplicación que tienen la misma dirección de correo electrónico que se muestran en sus contactos.


La detección de usuarios devolverá la siguiente información:

- **Identificador de registro de usuario** : el identificador único de un usuario en la base de datos pública.
- **Nombre y apellidos** : como se almacena en la base de datos pública.


Esta información solo se devolverá para los usuarios que hayan optado por la detección.

El código siguiente detectará información sobre el usuario que ha iniciado sesión actualmente en iCloud en el dispositivo:

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

Use el código siguiente para consultar todos los usuarios del libro de contactos:

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

En esta sección hemos tratado las cuatro áreas principales de acceso a la cuenta de un usuario que CloudKit puede proporcionar a una aplicación. Desde la obtención de la identidad y los metadatos del usuario, hasta las directivas de privacidad integradas en CloudKit y, por último, la capacidad de detectar otros usuarios de la aplicación.

## <a name="the-development-and-production-environments"></a>Entornos de desarrollo y producción

CloudKit proporciona entornos de desarrollo y de producción independientes para los datos y tipos de registro de una aplicación. El entorno de desarrollo es un entorno más flexible que solo está disponible para los miembros de un equipo de desarrollo. Cuando una aplicación agrega un nuevo campo a un registro y lo guarda en el entorno de desarrollo, el servidor actualiza automáticamente la información del esquema.

El desarrollador puede usar esta característica para realizar cambios en un esquema durante el desarrollo, lo que ahorra tiempo. Una advertencia es que, una vez agregado un campo a un registro, el tipo de datos asociado a ese campo no se puede cambiar mediante programación. Para cambiar el tipo de un campo, el desarrollador debe eliminar el campo en el [Panel de CloudKit](https://icloud.developer.apple.com/dashboard/) y agregarlo de nuevo con el nuevo tipo.

Antes de implementar la aplicación, el desarrollador puede migrar el esquema y los datos al entorno de producción mediante el **Panel de CloudKit**. Cuando se ejecuta en el entorno de producción, el servidor impide que una aplicación cambie el esquema mediante programación. El desarrollador todavía puede realizar cambios en el **Panel de CloudKit** , pero los intentos de agregar campos a un registro en el entorno de producción producen errores.

> [!NOTE]
> El simulador de iOS solo funciona con el **entorno de desarrollo**. Cuando el desarrollador está listo para probar una aplicación en un **entorno de producción**, se requiere un dispositivo iOS físico.


## <a name="shipping-a-cloudkit-enabled-app"></a>Envío de una aplicación habilitada para CloudKit

Antes de enviar una aplicación que usa CloudKit, debe configurarse para que tenga como destino el **entorno de CloudKit de producción** o Apple rechazará la aplicación.

Haga lo siguiente:

1. En Visual Studio para MA, compile la aplicación para el**dispositivo iOS**de **lanzamiento** > : 

    [![](intro-to-cloudkit-images/shipping01.png "Compilar la aplicación para su lanzamiento")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. En el menú compilar, seleccione **archivar**: 

    [![](intro-to-cloudkit-images/shipping02.png "Seleccionar archivo")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. El **archivo** se creará y se mostrará en Visual Studio para Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "Se creará y mostrará el archivo.")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Inicie **Xcode**.
5. En el menú **ventana** , seleccione **organizador**: 

    [![](intro-to-cloudkit-images/shipping04.png "Seleccionar organizador")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Seleccione el archivo de la aplicación y haga clic en el botón **exportar...** : 

    [![](intro-to-cloudkit-images/shipping05.png "El archivo de la aplicación")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Seleccione un método para la exportación y haga clic en el botón **siguiente** : 

    [![](intro-to-cloudkit-images/shipping06.png "Seleccionar un método para la exportación")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Seleccione el **equipo de desarrollo** en la lista desplegable y haga clic en el botón **elegir** : 

    [![](intro-to-cloudkit-images/shipping07.png "Seleccione el equipo de desarrollo en la lista desplegable.")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Seleccione **producción** en la lista desplegable y haga clic en el botón **siguiente** : 

    [![](intro-to-cloudkit-images/shipping08.png "Seleccionar producción en la lista desplegable")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Revise la configuración y haga clic en el botón **exportar** : 

    [![](intro-to-cloudkit-images/shipping09.png "Revisar la configuración")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Elija una ubicación para generar el archivo de `.ipa` aplicación resultante.

El proceso es similar para enviar la aplicación directamente a iTunes Connect, solo tiene que hacer clic en el botón **submit... (enviar...** ) en lugar de la exportación... después de seleccionar un archivo en la ventana del organizador.

## <a name="when-to-use-cloudkit"></a>Cuándo usar CloudKit

Como hemos aprendido en este artículo, CloudKit proporciona una manera sencilla para que una aplicación almacene y recupere información de los servidores de iCloud. Dicho esto, CloudKit no está obsoleto ni deja de usar ninguna de las herramientas o marcos de trabajo existentes.

### <a name="use-cases"></a>Casos de uso

Los siguientes casos de uso deben ayudar al desarrollador a decidir cuándo usar una tecnología o un marco de trabajo de iCloud específico:

- **el almacén de clave-valor de icloud** : mantiene de forma asincrónica una pequeña cantidad de datos actualizada y es excelente para trabajar con preferencias de la aplicación. Sin embargo, está restringido a una cantidad muy pequeña de información.
- **ICloud Drive** : se basa en las API de documentos de icloud existentes y proporciona una API simple para sincronizar datos no estructurados desde el sistema de archivos. Proporciona una memoria caché sin conexión completa en Mac OS X y es ideal para aplicaciones centradas en documentos.
- **datos principales de icloud** : permite que los datos se repliquen entre todos los dispositivos del usuario. Los datos son de usuario único y perfectos para mantener los datos estructurados privados sincronizados.
- **CloudKit** : proporciona datos públicos tanto estructura como masiva y es capaz de controlar tanto archivos grandes como no estructurados. Su asociado a la cuenta de iCloud del usuario y proporciona la transferencia de datos dirigidos por el cliente.


Teniendo en cuenta estos casos de uso, el desarrollador debe elegir la tecnología de iCloud correcta para proporcionar la funcionalidad de la aplicación necesaria actual y proporcionar una buena escalabilidad para el crecimiento futuro.

## <a name="summary"></a>Resumen

En este artículo se ha tratado una introducción rápida a la API de CloudKit. Ha mostrado cómo aprovisionar y configurar una aplicación de Xamarin iOS para usar CloudKit. Ha tratado las características de la API de CloudKit Practice. Muestra cómo diseñar una aplicación habilitada para CloudKit para la escalabilidad mediante consultas y suscripciones. Y, por último, ha mostrado la información de la cuenta de usuario que se expone a una aplicación mediante CloudKit.

## <a name="related-links"></a>Vínculos relacionados

- [CloudKitAtlas (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Creación de un perfil de aprovisionamiento](~/ios/get-started/installation/device-provisioning/index.md)
