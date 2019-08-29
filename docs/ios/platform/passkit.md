---
title: PassKit en Xamarin. iOS
description: La aplicación Wallet permite a los usuarios de iOS almacenar tarjetas digitales en sus dispositivos. El marco de PassKit permite a los desarrolladores interactuar con las tarjetas mediante programación.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: c434c39c12403bfc61e146041eb2a0780f29eb3e
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119182"
---
# <a name="passkit-in-xamarinios"></a>PassKit en Xamarin. iOS

La aplicación de iOS Wallet permite a los usuarios almacenar tarjetas digitales en sus dispositivos.
Estos pasos los generan los comerciantes y se envían al cliente por correo electrónico, direcciones URL o a través de la propia aplicación de iOS del comerciante. Estas pasadas pueden representar varias cosas, desde vales de película hasta tarjetas de fidelización hasta pasadas de incorporación. El marco de PassKit permite a los desarrolladores interactuar con las tarjetas mediante programación.

En este documento se presenta Wallet y el uso de la API de PassKit con Xamarin. iOS.

 [![](passkit-images/image1.png "Wallet almacena y organiza todas las pasadas en un teléfono")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

Las características de PassKit descritas en este documento requieren iOS 6 y Xcode 4,5, junto con Xamarin. iOS 6,0.

## <a name="introduction"></a>Introducción

El problema clave que resuelve PassKit es la distribución y la administración de códigos de barras. Estos son algunos ejemplos del mundo real de cómo se usan los códigos de barras:

- **Compra de entradas de cine en línea**: los clientes suelen recibir un correo electrónico con un código de barras que representa sus entradas. Este código de barras se imprime y se lleva al cine para digitalizarlo en la taquilla.
- **Tarjetas de fidelización**: los clientes llevan varias tarjetas diferentes específicas de cada tienda en la cartera o el bolso, para mostrar y digitalizar cuando compran productos.
- **Cupones**: los cupones se distribuyen a través del correo electrónico, como páginas web imprimibles, a través de buzones de correo y como códigos de barras en periódicos y revistas. Los clientes pueden llevarlos a una tienda y digitalizarlos para recibir productos, servicios o descuentos a cambio.
- **Fases de incorporación** : similar a la compra de un vale de película.

PassKit ofrece una alternativa para cada uno de estos escenarios:

- **Entradas de cine**: después de comprarla, el cliente agrega la tarjeta de la entrada de un evento (mediante el correo electrónico o un vínculo de un sitio web). Conforme se acerque la hora de la película, la tarjeta aparecerá automáticamente en la pantalla de bloqueo como recordatorio y, al llegar al cine, la tarjeta se puede recuperar y mostrar fácilmente en Wallet para que la digitalicen.
- **Tarjetas de fidelización**: en lugar de (o además de) proporcionar una tarjeta física, las tiendas pueden emitir (mediante correo electrónico o tras iniciar sesión en un sitio web) una tarjeta digital de la tienda. La tienda puede proporcionar características adicionales, como actualizar el saldo de la cuenta en la tarjeta mediante notificaciones de inserción y, mediante los servicios de geolocalización, la tarjeta podría aparecer automáticamente en la pantalla de bloqueo cuando el cliente esté cerca de la ubicación de una tienda.
- **Cupones**: se pueden generar fácilmente tarjetas de cupones con características únicas para ayudar a realizar el seguimiento y se pueden distribuir mediante vínculos de correo electrónico o sitios web. Los cupones descargados pueden aparecer automáticamente en la pantalla de bloqueo cuando el usuario esté cerca de una ubicación específica o en una fecha determinada (por ejemplo, cuando se aproxime la fecha de expiración). Dado que los cupones se almacenan en el teléfono del usuario, están siempre a mano y no se pierden. Los cupones pueden animar a los clientes a descargar aplicaciones complementarias porque los vínculos del App Store se pueden incorporar en la tarjeta, lo que aumenta la interacción con el cliente.
- **Tarjetas de embarque**: después de un proceso de facturación en línea, el cliente recibiría la tarjeta de embarque mediante correo electrónico o un vínculo. Una aplicación complementaria proporcionada por el proveedor de transporte podría incluir el proceso de facturación y permitir también al cliente realizar funciones adicionales, como elegir su asiento o comida. El proveedor de transporte puede usar notificaciones de inserción para actualizar la tarjeta si el transporte se retrasa o cancela. Conforme se acerque la hora del embarque, la tarjeta aparecerá en la pantalla de bloqueo como recordatorio y para proporcionar un acceso rápido a ella.

En esencia, PassKit proporciona una forma sencilla y cómoda de almacenar y mostrar códigos de barras en el dispositivo iOS. Con el tiempo adicional y la integración de la pantalla de bloqueo de ubicación, las notificaciones de inserción y la aplicación complementaria lo integran ofrece una base para servicios de ventas, vales y facturación muy sofisticados.

## <a name="passkit-ecosystem"></a>Ecosistema PassKit

PassKit no es solo una API dentro de CocoaTouch, sino que forma parte de un ecosistema mayor de aplicaciones, datos y servicios que facilitan el uso compartido y la administración seguros de códigos de barras y otros datos. En este diagrama de alto nivel se muestran las distintas entidades que pueden participar en la creación y el uso de las pasadas:

 [![](passkit-images/image2.png "En este diagrama de alto nivel se muestran las entidades implicadas en la creación y el uso de las pasadas.")](passkit-images/image2.png#lightbox)

Cada parte del ecosistema tiene un rol claramente definido:

- **Wallet**: aplicación de iOS integrada de Apple que almacena y muestra tarjetas. Este es el único lugar en que se representan las tarjetas para usarlas en el mundo real (es decir, se muestra el código de barras junto con todos los datos localizados en la tarjeta).
- **Aplicaciones complementarias**: aplicaciones de iOS 6 compiladas por proveedores de tarjetas para ampliar la funcionalidad de las tarjetas que emiten, como dar un valor añadido a la tarjeta de una tienda, cambiar el asiento en una tarjeta de embarque u otra función específica del negocio. Las aplicaciones complementarias no son necesarias para que una tarjeta sea útil.
- **Su servidor**: un servidor seguro donde se pueden generar y firmar las tarjetas para distribuirse. Su aplicación complementaria puede conectarse al servidor para generar nuevas tarjetas o solicitar actualizaciones de tarjetas existentes. Si quiere, también puede implementar la API de servicio web a la que llamaría Wallet para actualizar las tarjetas.
- **Servidores de APNS**: el servidor tiene la capacidad de notificar a Wallet las actualizaciones de una tarjeta en un dispositivo determinado con APNS. Envíe una notificación de inserción a Wallet, que después se pondrá en contacto con el servidor para obtener detalles del cambio. Las aplicaciones complementarias no tienen que implementar APNS para esta característica (pueden escuchar la notificación `PKPassLibraryDidChangeNotification`).
- **Conduit Apps**: aplicaciones que no manipulan directamente las tarjetas (como ocurre con las aplicaciones complementarias), pero que pueden mejorar su utilidad al reconocer las tarjetas y permitir que se agreguen a Wallet. Los clientes de correo electrónico, exploradores de redes sociales y otras aplicaciones de agregación de datos pueden encontrar datos adjuntos o vínculos a las tarjetas.

Todo el ecosistema tiene un aspecto complejo, por lo que merece la pena tener en cuenta que algunos componentes son opcionales y son posibles implementaciones de PassKit mucho más sencillas.

## <a name="what-is-a-pass"></a>¿Qué es una tarjeta?

Una tarjeta es una colección de datos que representan una entrada, un cupón o una tarjeta. Puede estar concebida para un solo uso de una persona (y, por tanto, contener detalles como el número de un vuelo y la asignación del asiento) o puede ser un token de varios usos que pueden compartir varios usuarios (por ejemplo, un cupón de descuento). Hay disponible una descripción detallada en el documento de Apple [About Pass Files](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) (Información sobre los archivos de tarjetas).

### <a name="types"></a>Tipos

Actualmente, cinco tipos admitidos, que se pueden distinguir en la aplicación Wallet por el diseño y el borde superior de la pasada:

- **Entrada de evento**: pequeño recorte semicircular.
- **Tarjeta de embarque**: muescas en los lados, se puede especificar el icono específico del transporte (por ejemplo, bus, tren o avión). Bus, tren, avión).
- **Tarjeta de tienda**: parte superior redondeada, como una tarjeta de crédito o débito.
- **Cupón** : perforado a lo largo de la parte superior.
- **Genérica**: igual que la tarjeta de tienda, parte superior redondeada.


Los cinco tipos de tarjetas se muestran en esta captura de pantalla (en orden: cupón, genérica, tarjeta de tienda, tarjeta de embarque y entrada de evento):

 [![](passkit-images/image3.png "En esta captura de pantalla se muestran los cinco tipos de pases")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estructura de archivos

Un archivo de tarjeta es realmente un archivo ZIP con una extensión **.pkpass** que contiene algunos archivos JSON específicos (obligatorios), una variedad de archivos de imagen (opcionales), así como las cadenas localizadas (también opcionales).

- **pass.json**: obligatorio. Contiene toda la información de la tarjeta.
- **manifest.json**: obligatorio. Contiene los hashes de SHA1 de cada archivo de la tarjeta, excepto el archivo de firma y este archivo (manifest.json).
- **Signature** : requerido. Se crea firmando `manifest.json` el archivo con el certificado generado en el portal de aprovisionamiento de iOS.
- **logo. png** : opcional.
- **background. png** : opcional.
- **Icon. png** : opcional.
- **Archivos de cadenas localizables** : opcional.

A continuación se muestra la estructura de directorios de un archivo de tarjeta (este es el contenido del archivo ZIP):

 [![](passkit-images/image4.png "Aquí se muestra la estructura de directorios de un archivo de paso")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON es el formato porque normalmente se crean pasadas en un servidor, lo que significa que el código de generación es independiente de la plataforma en el servidor. Las tres partes clave de la información en cada paso son:

- **teamIdentifier** : esta vincula todos los pasos que genere en la cuenta de App Store. Este valor es visible en el portal de aprovisionamiento de iOS.
- **passTypeIdentifier** : Regístrese en el portal de aprovisionamiento para agrupar las pasadas (si genera más de un tipo). Por ejemplo, una cafetería podría crear un tipo de pase de tarjeta de tienda para permitir que sus clientes obtengan créditos de fidelidad, pero también un tipo de paso de cupón independiente para crear y distribuir cupones de descuento. Esa misma cafetería podría incluso contener eventos de música en directo y emitir pasadas de incidencias de eventos para ellos.
- **SerialNumber** : una cadena única dentro de `passTypeidentifier` este. El valor es opaco a Wallet, pero es importante para realizar un seguimiento de los pasos específicos al comunicarse con el servidor.

Hay muchas otras claves JSON en cada tarjeta; se muestra un ejemplo de ellas a continuación:

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>Códigos

Solo se admiten los formatos 2D: PDF417, Aztec, QR. Apple notifica que los códigos de barras 1D están desactivados para digitalizar en una pantalla de teléfono con retroiluminación.

El texto alternativo que se muestra debajo del código de barras es opcional; algunos comerciantes quieren ser capaces de leer o escribir manualmente.

La codificación ISO-8859-1 es la más común, compruebe qué codificación utilizan los sistemas de análisis que leerán las pasadas.

### <a name="relevancy-lock-screen"></a>Relevancia (pantalla de bloqueo)

Hay dos tipos de datos que pueden hacer que se muestre un paso en la pantalla de bloqueo:

 **Ubicación**

Se pueden especificar hasta 10 ubicaciones en una tarjeta, por ejemplo, las tiendas que un cliente visita con frecuencia o la ubicación de un cine o un aeropuerto. Un cliente podría especificar estas ubicaciones mediante una aplicación complementaria o el proveedor podría determinarlas en función de los datos de uso (si se recopilan con el permiso del cliente).

Cuando la tarjeta se muestra en la pantalla de bloqueo, se calcula una barrera para que, cuando el usuario abandone la zona, la tarjeta desaparezca de la pantalla de bloqueo. El radio está asociado con el estilo de tarjeta para evitar abusos.

 **Fecha y hora**

En una tarjeta, solo se puede especificar una fecha y hora. La fecha y hora es útil para desencadenar recordatorios en la pantalla de bloqueo en el caso de las tarjetas de embarque y las entradas de eventos.

Se puede actualizar a través de la inserciones o a través de la API de PassKit, para que la fecha y la hora se puedan actualizar en el caso de una incidencia de varios uso (por ejemplo, un vale de temporada a un teatro o un complejo deportivo).

### <a name="localization"></a>Localización

Traducir un paso en varios idiomas es similar a la localización de una aplicación de iOS: crear directorios específicos de idioma `.lproj` con la extensión y colocar los elementos localizados dentro de. Las traducciones de texto deben introducirse `pass.strings` en un archivo, mientras que las imágenes localizadas deben tener el mismo nombre que la imagen que reemplazan en la raíz del paso.

## <a name="security"></a>Seguridad

Las tarjetas se firman con un certificado privado que genera en el Portal de aprovisionamiento de iOS. Los pasos para firmar la tarjeta son:

1. Calcule un hash SHA1 para cada archivo en el directorio de la tarjeta (no incluya el archivo `manifest.json` ni el archivo `signature`; ninguno de ellos debe existir en esta fase de todos modos).
1. Escriba `manifest.json` como una lista de clave/valor JSON de cada nombre de archivo con su hash.
1. Utilice el certificado para firmar el `manifest.json` archivo y escribir el resultado en un archivo denominado `signature` .
1. Comprimir todo y dar al archivo resultante una `.pkpass` extensión de archivo.


Dado que la clave privada se necesita para firmar la tarjeta, este proceso solo debe realizarse en un servidor seguro que controle. NO distribuya las claves para probar y generar tarjetas en una aplicación.

 
## <a name="configuration-and-setup"></a>Configuración y configuración

Esta sección contiene instrucciones para ayudar a configurar los detalles de aprovisionamiento y crear el primer paso.

### <a name="provisioning-passkit"></a>Aprovisionamiento de PassKit

Para que una tarjeta entre en el App Store, se debe vincular a una cuenta de desarrollador. Esto requiere dos pasos:

1. La tarjeta debe estar registrada con un identificador único, que se denomina Pass Type ID (identificador del tipo de tarjeta).
1. Se debe generar un certificado válido para firmar la tarjeta con la firma digital del desarrollador.

Para crear un Pass Type ID (identificador del tipo de tarjeta), realice lo siguiente.

#### <a name="create-a-pass-type-id"></a>Crear un ID. de tipo de paso

El primer paso es configurar un Pass Type ID (identificador del tipo de tarjeta) para que se admitan todos los _tipos_ diferentes de tarjeta. El Pass ID (identificador de tarjeta) o el Pass Type identifier (identificador del tipo de tarjeta) crea un identificador único de la tarjeta. Usaremos este identificador para vincular la tarjeta con su cuenta de desarrollador mediante un certificado.

1. En la sección [Certificates, Identifiers, and Profiles (Certificados, identificadores y perfiles) del Portal de aprovisionamiento de iOS](https://developer.apple.com/account/overview.action), vaya a **Identifiers** (Identificadores) y seleccione **Pass Type IDs** (Identificadores del tipo de tarjeta). Después, seleccione **+** el botón para crear un nuevo tipo de paso: [![](passkit-images/passid.png "Crear un nuevo tipo de paso")](passkit-images/passid.png#lightbox)

2. Proporcione una **Descripción** (nombre) y un **Identificador** (cadena única) para la tarjeta. Tenga en cuenta que todos los identificadores de tipo de `pass.` paso deben comenzar con la `pass.com.xamarin.coupon.banana` cadena en este ejemplo, se usa: [![](passkit-images/register.png "Proporcionar una descripción y un identificador")](passkit-images/register.png#lightbox)


3. Confirme el ID. de paso presionando el botón **registrar** .

#### <a name="generate-a-certificate"></a>Generar un certificado

Para crear un nuevo certificado para este identificador de tipo de paso, haga lo siguiente:

1. Seleccione el ID. de paso recién creado en la lista y haga clic en **Editar** : [![](passkit-images/pass-done.png "Seleccione el nuevo ID. de paso de la lista")](passkit-images/pass-done.png#lightbox)

    A continuación, seleccione **crear certificado..** . :

    [![](passkit-images/cert-dist.png "Seleccione Crear certificado.")](passkit-images/cert-dist.png#lightbox)


2. Siga los pasos para crear una solicitud de firma de certificado (CSR).
  
3. Presione el botón **continuar** en el portal para desarrolladores y cargue el CSR para generar el certificado.

4. Descargue el certificado y haga doble clic en él para instalarlo en la cadena de claves.


Ahora que hemos creado un certificado para este Pass Type ID (identificador del tipo de tarjeta), en la siguiente sección se describe cómo crear una tarjeta manualmente.

Para obtener más información sobre el aprovisionamiento de Wallet, consulte la guía [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) .

### <a name="create-a-pass-manually"></a>Crear un paso manualmente

Ahora que hemos creado el tipo de tarjeta, podemos crear manualmente una tarjeta para probarla en el simulador o en un dispositivo. Los pasos para crear una tarjeta son:

- Cree un directorio que contenga los archivos de tarjeta.
- Cree un archivo Pass. JSON que contenga todos los datos necesarios.
- Incluir imágenes en la carpeta (si es necesario).
- Calcule los hashes SHA1 para cada archivo de la carpeta y escriba en manifest. JSON.
- Firme manifest. JSON con el archivo Certificate. p12 descargado.
- Comprima el contenido del directorio y cámbiele el nombre para que incluya la extensión .pkpass.


Hay algunos archivos de origen en el [código de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) de este artículo que se pueden usar para generar una tarjeta. Use los archivos del directorio `CouponBanana.raw` del directorio CreateAPassManually. Aparecen los siguientes archivos:

 [![](passkit-images/image18.png "Estos archivos están presentes")](passkit-images/image18.png#lightbox)

Abra Pass. JSON y edite el archivo JSON. Debe actualizar al menos el `passTypeIdentifier` y `teamIdentifer` para que coincida con su cuenta de desarrollador de Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

A continuación, debe calcular los valores hash de cada archivo y crear `manifest.json` el archivo. Tendrá un aspecto similar al siguiente cuando haya terminado:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

A continuación, se debe generar una firma para este archivo mediante el certificado (archivo. p12) generado para este identificador de tipo de paso.

#### <a name="signing-on-a-mac"></a>Iniciar sesión en un equipo Mac

Descargue **Wallet Seed Support Materials** (Materiales de soporte técnico de inicialización de Wallet) en el sitio de [descargas de Apple](https://developer.apple.com/downloads/index.action?name=Passbook). Use la herramienta `signpass` para convertir la carpeta en una tarjeta (esto también calculará los hashes de SHA1 y comprimirá la salida en un archivo .pkpass).

#### <a name="testing"></a>Pruebas

Si va a examinar la salida de estas herramientas (estableciendo el nombre de archivo en. zip y abriéndolo), verá los siguientes archivos (tenga en cuenta la adición de los `manifest.json` archivos y `signature` ):

 [![](passkit-images/image19.png "Examinar la salida de estas herramientas")](passkit-images/image19.png#lightbox)

Una vez que haya firmado, comprimido y cambiado el nombre del archivo (por ejemplo, a `BananaCoupon.pkpass`) puede arrastrarlo al simulador para probarlo o enviarlo por correo electrónico para que lo recupere en un dispositivo real. Debería ver una pantalla para **Agregar** el paso, de la siguiente manera:

 [![](passkit-images/image20.png "Agregar la pantalla de paso")](passkit-images/image20.png#lightbox)

Normalmente, ese proceso se automatizaría en un servidor, pero la creación de paso manual podría ser una opción para las pequeñas empresas que solo crean cupones que no requieren la compatibilidad de un servidor back-end.

## <a name="wallet"></a>Cartera

Wallet es la parte central del ecosistema de PassKit. En esta captura de pantalla se muestra Wallet vacío, así como el aspecto de la lista de tarjetas y las tarjetas individuales:

 [![](passkit-images/image21.png "En esta captura de pantalla se muestra Wallet vacío, así como el aspecto de la lista de tarjetas y las tarjetas individuales")](passkit-images/image21.png#lightbox)

Las características de Wallet incluyen:

- Es el único lugar en el que se representan las tarjetas con su código de barras para digitalizarlas.
- El usuario puede cambiar la configuración de las actualizaciones. Si están habilitadas, las notificaciones de inserción pueden desencadenar actualizaciones en los datos de la tarjeta.
- El usuario puede habilitar o deshabilitar la integración con la pantalla de bloqueo. Si está habilitada, esto permite que la tarjeta aparezca automáticamente en la pantalla de bloqueo, según los datos de ubicación y hora pertinentes insertados en la tarjeta.
- El lado inverso de la pasada admite la extracción a la actualización, si se proporciona una dirección URL de Web-Server en el JSON de Pass.
- Las aplicaciones complementarias se pueden abrir (o descargar) si el identificador de la aplicación se proporciona en el JSON de Pass.
- Las tarjetas pueden eliminarse (con una bonita animación de trituración).

## <a name="adding-passes-into-wallet"></a>Agregar pasadas a Wallet

Pueden agregarse tarjetas a Wallet de las maneras siguientes:

- **Conduit Apps**: estas no manipulan directamente las tarjetas, simplemente cargan archivos de tarjetas y muestran al usuario la opción de agregarlas a Wallet. 

- **Aplicaciones complementarias**: estas las escriben los proveedores para distribuir tarjetas y ofrecer funcionalidades adicionales para examinarlas o modificarlas. Las aplicaciones de Xamarin.iOS tienen acceso completo a la API de PassKit para crear y manipular tarjetas. Después, se pueden agregar tarjetas a Wallet mediante `PKAddPassesViewController`. Este proceso se describe con más detalles en la sección **Aplicaciones complementarias** de este documento.

### <a name="conduit-applications"></a>Aplicaciones de conducto

Las aplicaciones de canalización son aplicaciones intermediarias que pueden recibir tarjetas en nombre de un usuario y deben programarse para reconocer su tipo de contenido y proporcionar funcionalidades para agregarlas a Wallet. Algunos ejemplos de aplicaciones de canalización son:

- **Mail**: reconoce los datos adjuntos como una tarjeta.
- **Safari**: reconoce el tipo de contenido de la tarjeta cuando se hace clic en un vínculo de URL de tarjeta.
- **Otras aplicaciones personalizadas** : cualquier aplicación que reciba datos adjuntos o vínculos abiertos (clientes de medios sociales, lectores de correo, etc.).


En esta captura de pantalla se muestra cómo **Mail** en iOS 6 reconoce un archivo adjunto de tarjeta y (cuando se toca) ofrece la posibilidad de **Agregarlo** a Wallet.

 [![](passkit-images/image22.png "En esta captura de pantalla se muestra cómo mail in iOS 6 reconoce un archivo adjunto de Pass")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "En esta captura de pantalla se muestra cómo proporciona correo electrónico para agregar datos adjuntos de Pass a Wallet")](passkit-images/image23.png#lightbox)

Si va a compilar una aplicación que podría ser una canalización para tarjetas, pueden reconocerse por:

- **Extensión de archivo** :. pkpass
- **Tipo MIME** : aplicación/vnd. Apple. pkpass
- **UTI** – com.apple.pkpass


El funcionamiento básico de una aplicación de canalización es recuperar el archivo de tarjeta y llamar al controlador `PKAddPassesViewController` de PassKit para proporcionar al usuario la opción de agregar la tarjeta a Wallet. La implementación de este controlador de vista se trata en la siguiente sección en **Aplicaciones complementarias**.

No es necesario aprovisionar aplicaciones de conducto para un identificador de tipo de paso específico de la misma manera que lo hacen las aplicaciones complementarias.

## <a name="companion-applications"></a>Aplicaciones complementarias

Una aplicación complementaria proporciona funcionalidades adicionales para trabajar con tarjetas; por ejemplo, crear una tarjeta, actualizar la información asociada con una tarjeta y administrar de otro modo las tarjetas asociadas con la aplicación.

Las aplicaciones complementarias no deben intentar duplicar las características de wallet. Su finalidad no es mostrar tarjetas para digitalizarlas.

En el resto de esta sección se describe cómo crear una aplicación complementaria básica que interactúe con PassKit.

### <a name="provisioning"></a>Aprovisionamiento

Dado que Wallet es una tecnología de tienda, la aplicación debe aprovisionarse por separado y no puede usar el perfil de aprovisionamiento de equipo o el identificador de aplicación comodín. Consulte la guía [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) para crear un identificador de aplicación único y un perfil de aprovisionamiento para la aplicación wallet.

### <a name="entitlements"></a>Derechos

El archivo contitles **. plist** debe estar incluido en todos los proyectos de Xamarin. iOS recientes. Para agregar un nuevo archivo contitles. plist, siga los pasos de la guía [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) .

Para establecer los derechos, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Haga doble clic en el archivo contitles **. plist** en el panel de solución para abrir el editor de derechos. plist:

![](passkit-images/image31.png "Contitles. Editor plst")

En la sección Wallet, seleccione la opción **Habilitar Wallet** .

![](passkit-images/image32.png "Habilitar derechos de Wallet")


La opción predeterminada es que la aplicación permita todos los tipos de tarjeta. En cambio, es posible restringir la aplicación y permitir solo un subconjunto de tipos de tarjeta de equipo. Para habilitarlo, seleccione **Permitir un subconjunto de tipos de pase de equipo** y escriba el identificador del tipo de tarjeta del subconjunto que quiera permitir.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Haga doble clic en el archivo contitles **. plist** para abrir el archivo de origen XML.

Para agregar el derecho de Wallet, establezca la propiedad `Passbook Identifiers` en en la lista desplegable, que establecerá automáticamente el **tipo** `Array`. A continuación, establezca el **valor** de `$(TeamIdentifierPrefix)*`cadena en:

![](passkit-images/image33.png "Habilitar derechos de Wallet")

De esta manera, la aplicación admitirá todos los tipos de pases. Para restringir la aplicación y permitir solo un subconjunto de tipos de pases de equipo, establezca el valor de cadena en:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Donde `pass.$(CFBundleIdentifier)` es el identificador de la tarjeta que se ha creado [anteriormente](~/ios/platform/passkit.md).

-----

### <a name="debugging"></a>Depuración

Si tiene problemas para implementar la aplicación, compruebe que está usando el **Perfil de aprovisionamiento** correcto y `Entitlements.plist` que está seleccionado como el archivo de **derechos personalizados** en las opciones de **firma de paquete de iPhone** .

Si experimenta este error al implementar:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

entonces la matriz de derechos `pass-type-identifiers` es incorrecta (o no coincide con el **perfil de aprovisionamiento**). Compruebe que los Pass Type ID (identificadores del tipo de tarjeta) y el Team ID (identificador del equipo) son correctos.

## <a name="classes"></a>Clases

Las siguientes clases de PassKit están disponibles para que las aplicaciones tengan acceso a los pasos:

- **PKPass**: una instancia de una tarjeta.
- **PKPassLibrary**: proporciona la API para acceder a las tarjetas en el dispositivo.
- **PKAddPassesViewController**: se usa para mostrar una tarjeta para que el usuario la guarde en Wallet.
- **PKAddPassesViewControllerDelegate** : desarrolladores de Xamarin. iOS

## <a name="example"></a>Ejemplo

Consulte el proyecto PassLibrary en el [código de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) de este artículo. Muestra las siguientes funciones comunes que serían necesarias en una aplicación complementaria de Wallet:

### <a name="check-that-wallet-is-available"></a>Comprobar que Wallet está disponible

Wallet no está disponible en el iPad, por lo que las aplicaciones deben comprobarse antes de intentar acceder a las características de PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Creación de una instancia de biblioteca de paso

La biblioteca PassKit no es un singleton, las aplicaciones deben crear y almacenar e instancia para acceder a la API de PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obtener una lista de las pasadas

Las aplicaciones pueden solicitar una lista de tarjetas de la biblioteca. PassKit filtra esta lista automáticamente para que solo pueda ver las tarjetas que se han creado con su Team ID (identificador del equipo) y que aparecen en la lista Derechos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Tenga en cuenta que el simulador no filtra la lista de las pasadas devueltas, por lo que este método siempre debe probarse en dispositivos reales. Esta lista se puede mostrar en un UITableView. La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) tiene el siguiente aspecto después de haber agregado dos cupones:

 [![](passkit-images/image29.png "La aplicación de ejemplo tiene el siguiente aspecto después de haber agregado dos cupones")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Mostrar pasadas

Hay disponible un conjunto limitado de información para representar tarjetas en las aplicaciones complementarias.

Elija entre este conjunto de propiedades estándar para mostrar listas de tarjetas, como hace el código de ejemplo.

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

Esta cadena se muestra como una alerta en el [ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit):

 [![](passkit-images/image30.png "La alerta de cupón seleccionada en el ejemplo")](passkit-images/image30.png#lightbox)

También puede usar el método `LocalizedValueForFieldKey()` para recuperar datos de los campos de las tarjetas que ha diseñado (puesto que sabrá qué campos deben aparecer). En el código de ejemplo no se muestra esto.

### <a name="loading-a-pass-from-a-file"></a>Cargar un paso desde un archivo

Dado que solo se puede Agregar un paso a Wallet con el permiso del usuario, se debe presentar un controlador de vista para permitirle decidir. Este código se usa en el botón **Agregar** del ejemplo para cargar un paso pregenerado que está incrustado en la aplicación (debe reemplazarlo por uno que haya firmado):

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

El paso se presenta con las opciones **Agregar** y **Cancelar** :

 [![](passkit-images/image20.png "El paso presentado con las opciones Agregar y cancelar")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Reemplazar un paso existente

Si se reemplaza un paso existente, no se requiere el permiso del usuario; sin embargo, se producirá un error si no existe el paso.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Edición de un paso

PKPass no es mutable, por lo que no puede actualizar los objetos de tarjeta en el código. Para modificar los datos de una tarjeta, una aplicación debe tener acceso a un servidor web que pueda mantener un registro de las tarjetas y generar un nuevo archivo de tarjeta con valores actualizados que la aplicación pueda descargar.

La creación del archivo de tarjeta debe realizarse en un servidor porque las tarjetas deben firmarse con un certificado que se debe mantener privado y seguro.

Una vez que se ha generado un archivo de paso actualizado `Replace` , use el método para sobrescribir los datos antiguos en el dispositivo.

### <a name="display-a-pass-for-scanning"></a>Mostrar un paso para el examen

Como se indicó anteriormente, solo Wallet puede mostrar un paso para la digitalización. Se puede mostrar un paso mediante el `OpenUrl` método, como se muestra A continuación:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Recibir notificaciones de cambios

Las aplicaciones pueden escuchar los cambios realizados en la biblioteca de tarjetas mediante `PKPassLibraryDidChangeNotification`. Las notificaciones que desencadenan actualizaciones en segundo plano podrían ser la causa de los cambios, por lo que es recomendable escucharlos en la aplicación.

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

Es importante pasar una instancia de biblioteca al registrarse para la notificación porque PKPassLibrary no es un singleton.

## <a name="server-processing"></a>Procesamiento de servidor

Un análisis detallado de la creación de una aplicación de servidor para admitir PassKit está fuera del ámbito de este artículo introductorio.

Consulte [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook) código abierto C# del lado servidor.

## <a name="push-notifications"></a>Notificaciones push

Una explicación detallada del uso de notificaciones de extracción para actualizar los pasos queda fuera del ámbito de este artículo introductorio.

Sería necesario implementar la API de tipo REST definida por Apple para responder a las solicitudes Web de Wallet cuando se requieran actualizaciones.

Consulte la guía de [actualización de un paso](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) de Apple para obtener más información.

## <a name="summary"></a>Resumen

En este artículo se ha presentado PassKit, se han destacado algunas de las razones por las que es útil y se han descrito los distintos elementos que se deben implementar para tener una solución completa de PassKit. Se han descrito los pasos necesarios para configurar la cuenta de desarrollador de Apple a fin de crear tarjetas, el proceso para crear una tarjeta manualmente y también cómo acceder a las API de PassKit desde una aplicación de Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Wallet para desarrolladores](https://developer.apple.com/wallet/)
- [Ejemplo de PassKit](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)
- [Guía del desarrollador de Wallet](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Marcos de trabajo: Apple Pay y Wallet (vídeos de WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Referencia de PassKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Referencia del servicio Web de passbook](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [Acerca de los archivos de paso](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook), una biblioteca de código abierto para generar paquetes de Wallet de iOS
- [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
