---
title: PassKit en Xamarin. iOS
description: La aplicación Wallet permite que los usuarios de iOS almacenen pasadas digitales en sus dispositivos. El marco de PassKit permite a los programadores interactuar con los pasos mediante programación.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2018
ms.openlocfilehash: d5a4a78341f66ec754d9161e201023c0ebb478d4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939196"
---
# <a name="passkit-in-xamarinios"></a>PassKit en Xamarin. iOS

La aplicación de Wallet de iOS permite a los usuarios almacenar pasadas digitales en sus dispositivos.
Estos pasos los generan los comerciantes y se envían al cliente por correo electrónico, direcciones URL o a través de la propia aplicación de iOS del comerciante. Estas pasadas pueden representar varias cosas, desde vales de película hasta tarjetas de fidelización hasta pasadas de incorporación. El marco de PassKit permite a los programadores interactuar con los pasos mediante programación.

En este documento se presenta Wallet y el uso de la API de PassKit con Xamarin. iOS.

 [![Wallet almacena y organiza todas las pasadas en un teléfono](passkit-images/image1.png)](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

Las características de PassKit descritas en este documento requieren iOS 6 y Xcode 4,5, junto con Xamarin. iOS 6,0.

## <a name="introduction"></a>Introducción

El problema clave que resuelve PassKit es la distribución y la administración de códigos de barras. Estos son algunos ejemplos del mundo real de cómo se usan los códigos de barras:

- **Compra de tickets en línea** : a los clientes se les envía por correo electrónico un código de barras que representa sus vales. Este código de barras se imprime y se lleva al cine para que se examine.
- **Tarjetas de fidelización** : los clientes llevan una serie de tarjetas diferentes específicas del almacén en su cartera o monedero, para su visualización y análisis cuando compran productos.
- **Cupones** : los cupones se distribuyen por correo electrónico, como páginas web imprimibles, a través de panorámicas y como códigos de barras en periódicos y revistas. Los clientes los llevan a una tienda para su análisis, para recibir productos, servicios o descuentos en la devolución.
- **Fases de incorporación** : similar a la compra de un vale de película.

PassKit ofrece una alternativa para cada uno de estos escenarios:

- **Tickets de películas** : después de la compra, el cliente agrega un paso de vale de evento (por correo electrónico o un vínculo a un sitio web). En cuanto a la duración de la película, el paso aparecerá automáticamente en la pantalla de bloqueo como recordatorio y, cuando llegue a la Cinema, la pasada se recuperará y mostrará en Wallet para su análisis.
- **Tarjetas de fidelidad** : en lugar de (o además de) proporcionar una tarjeta física, las tiendas pueden emitir (por correo electrónico o después de un inicio de sesión en un sitio web) un paso de la tarjeta de la tienda. El almacén puede proporcionar características adicionales, como la actualización del saldo de la cuenta en el paso a través de las notificaciones de envío y el uso de servicios de ubicación geográfica, el paso podría aparecer automáticamente en la pantalla de bloqueo cuando el cliente está cerca de una ubicación de almacén.
- **Cupones: las** pasadas de cupón se pueden generar fácilmente con características únicas para ayudar con el seguimiento y distribuirse a través de vínculos de correo electrónico o sitios Web. Los cupones descargados pueden aparecer automáticamente en la pantalla de bloqueo cuando el usuario está cerca de una ubicación específica y/o en una fecha determinada (por ejemplo, cuando se está acercando a la fecha de expiración). Dado que los cupones se almacenan en el teléfono del usuario, siempre resultan prácticos y no se colocan. Los cupones pueden animar a los clientes a descargar aplicaciones complementarias, ya que los vínculos de la tienda de aplicaciones se pueden incorporar a la fase, lo que aumenta el compromiso con el cliente.
- **Fases** de la incorporación: después de un proceso de inserción en el repositorio en línea, el cliente recibirá el paso de la incorporación por correo electrónico o un vínculo. Una aplicación complementaria proporcionada por el proveedor de transporte podría incluir el proceso de inserción en el repositorio y permitir que el cliente realice funciones adicionales, como elegir su puesto o comida. El proveedor de transporte puede usar notificaciones de envío para actualizar el paso si el transporte se retrasa o se cancela. A medida que el tiempo de incorporación se aproxima al paso, aparecerá en la pantalla de bloqueo como recordatorio y para proporcionar acceso rápido a la fase.

En esencia, PassKit proporciona una forma sencilla y cómoda de almacenar y mostrar códigos de barras en el dispositivo iOS. Con el tiempo adicional y la integración de la pantalla de bloqueo de ubicación, las notificaciones de inserción y la aplicación complementaria lo integran ofrece una base para servicios de ventas, vales y facturación muy sofisticados.

## <a name="passkit-ecosystem"></a>Ecosistema PassKit

PassKit no es solo una API dentro de CocoaTouch, sino que forma parte de un ecosistema mayor de aplicaciones, datos y servicios que facilitan el uso compartido y la administración seguros de códigos de barras y otros datos. En este diagrama de alto nivel se muestran las distintas entidades que pueden participar en la creación y el uso de las pasadas:

 [![En este diagrama de alto nivel se muestran las entidades implicadas en la creación y el uso de las pasadas.](passkit-images/image2.png)](passkit-images/image2.png#lightbox)

Cada parte del ecosistema tiene un rol claramente definido:

- **Wallet** : aplicación iOS integrada de Apple que almacena y muestra las pasadas. Este es el único lugar en el que se representan las pasadas para su uso en el mundo real (es decir, se muestra el código de barras, junto con todos los datos localizados en el paso).
- **Aplicaciones complementarias** : aplicaciones de iOS 6 creadas por los proveedores de Pass para ampliar la funcionalidad de los pasos que emiten, como agregar valor a una tarjeta de la tienda, cambiar el puesto en un paso de incorporación o en otra función específica de la empresa. Las aplicaciones complementarias no son necesarias para que un paso sea útil.
- **El servidor** : servidor seguro en el que se pueden generar y firmar las pasadas para su distribución. La aplicación complementaria puede conectarse al servidor para generar nuevas pasadas o solicitar actualizaciones a las pasadas existentes. Opcionalmente, puede implementar la API del servicio Web a la que llamará Wallet para actualizar las pasadas.
- **Servidores de APNS** : el servidor tiene la capacidad de notificar a la cartera de actualizaciones a un paso en un dispositivo determinado mediante APNS. Inserte una notificación en Wallet que luego se pondrá en contacto con el servidor para obtener detalles del cambio. No es necesario que las aplicaciones complementarias implementen APNS para esta característica (pueden escuchar `PKPassLibraryDidChangeNotification` ).
- **Aplicaciones de conducto** : las aplicaciones que no manipulan los pasos directamente (como las aplicaciones complementarias sí), pero que pueden mejorar su utilidad al reconocer los pasos y permitir que se agreguen a wallet. Los clientes de correo, los exploradores de redes sociales y otras aplicaciones de agregación de datos pueden encontrar datos adjuntos o vínculos a las pasadas.

Todo el ecosistema tiene un aspecto complejo, por lo que merece la pena tener en cuenta que algunos componentes son opcionales y son posibles implementaciones de PassKit mucho más sencillas.

## <a name="what-is-a-pass"></a>¿Qué es un paso?

Un paso es una colección de datos que representa un vale, un cupón o una tarjeta. Puede estar pensada para un único uso por parte de un individuo (y, por lo tanto, contiene detalles como un número de vuelo y una asignación de puesto) o puede ser un token de uso múltiple que puede ser compartido por cualquier número de usuarios (como un cupón de descuento). Hay disponible una descripción detallada en el documento [acerca de los archivos de paso](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) de Apple.

### <a name="types"></a>Tipos

Actualmente, cinco tipos admitidos, que se pueden distinguir en la aplicación Wallet por el diseño y el borde superior de la pasada:

- **Vale de evento** : recorte semicircular pequeño.
- **Paso de incorporación** : se pueden especificar las muescas en el lado, el icono específico del transporte (por ejemplo, Bus, tren, avión).
- **Tarjeta de almacenamiento** : redondeando la parte superior, como una tarjeta de crédito o débito.
- **Cupón** : perforado a lo largo de la parte superior.
- **Genérico** : igual que la tarjeta de almacenamiento, la parte superior redondeada.

En esta captura de pantalla se muestran los cinco tipos de paso (en orden: cupón, genérico, tarjeta de almacén, paso de incorporación y vale de evento):

 [![En esta captura de pantalla se muestran los cinco tipos de pases](passkit-images/image3.png)](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estructura de archivos

Un archivo de paso es realmente un archivo ZIP con una extensión **. pkpass** , que contiene algunos archivos JSON específicos (obligatorio), una variedad de archivos de imagen (opcional), así como cadenas localizadas (también opcional).

- **pass.jsen** : requerido. Contiene toda la información del paso.
- **manifest.jsen** : requerido. Contiene hashes SHA1 para cada archivo del paso excepto el archivo de signatura y este archivo (manifest.jsen).
- **Signature** : requerido. Se crea firmando el `manifest.json` archivo con el certificado generado en el portal de aprovisionamiento de iOS.
- **logo.png** : opcional.
- **background.png** : opcional.
- **icon.png** : opcional.
- **Archivos de cadenas localizables** : opcional.

A continuación se muestra la estructura de directorios de un archivo de paso (este es el contenido del archivo ZIP):

 [![Aquí se muestra la estructura de directorios de un archivo de paso](passkit-images/image4.png)](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.jsen

JSON es el formato porque normalmente se crean pasadas en un servidor, lo que significa que el código de generación es independiente de la plataforma en el servidor. Las tres partes clave de la información en cada paso son:

- **teamIdentifier** : esta vincula todos los pasos que genere en la cuenta de App Store. Este valor es visible en el portal de aprovisionamiento de iOS.
- **passTypeIdentifier** : Regístrese en el portal de aprovisionamiento para agrupar las pasadas (si genera más de un tipo). Por ejemplo, una cafetería podría crear un tipo de pase de tarjeta de tienda para permitir que sus clientes obtengan créditos de fidelidad, pero también un tipo de paso de cupón independiente para crear y distribuir cupones de descuento. Esa misma cafetería podría incluso contener eventos de música en directo y emitir pasadas de incidencias de eventos para ellos.
- **SerialNumber** : una cadena única dentro de este `passTypeidentifier` . El valor es opaco a Wallet, pero es importante para realizar un seguimiento de los pasos específicos al comunicarse con el servidor.

Hay un gran número de otras claves JSON en cada paso, un ejemplo que se muestra a continuación:

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

### <a name="barcodes"></a>Códigos de barras

Solo se admiten formatos 2D: PDF417, Aztec y QR. Apple notifica que los códigos de barras 1D están desactivados para digitalizar en una pantalla de teléfono con retroiluminación.

El texto alternativo que se muestra debajo del código de barras es opcional; algunos comerciantes quieren ser capaces de leer o escribir manualmente.

La codificación ISO-8859-1 es la más común, compruebe qué codificación utilizan los sistemas de análisis que leerán las pasadas.

### <a name="relevancy-lock-screen"></a>Relevancia (pantalla de bloqueo)

Hay dos tipos de datos que pueden hacer que se muestre un paso en la pantalla de bloqueo:

 **Ubicación**

Se pueden especificar hasta 10 ubicaciones en un paso, p. ej., almacena que un cliente visita con frecuencia o la ubicación de una cine o aeropuerto. Un cliente podría establecer estas ubicaciones a través de una aplicación complementaria o el proveedor podría determinarlas a partir de datos de uso (si se recopilan con el permiso del cliente).

Cuando se muestra el paso en la pantalla de bloqueo, se calcula una barrera para que cuando el usuario abandone el área se oculte el paso de la pantalla de bloqueo. El radio está ligado al estilo de paso para evitar el abuso.

 **Fecha y hora**

Solo se puede especificar una fecha y hora en un paso. La fecha y la hora son útiles para desencadenar recordatorios de la pantalla de bloqueo para las fases de incorporación y los vales de eventos.

Se puede actualizar a través de la inserciones o a través de la API de PassKit, para que la fecha y la hora se puedan actualizar en el caso de una incidencia de varios uso (por ejemplo, un vale de temporada a un teatro o un complejo deportivo).

### <a name="localization"></a>Localización

Traducir un paso en varios idiomas es similar a la localización de una aplicación de iOS: crear directorios específicos de idioma con la `.lproj` extensión y colocar los elementos localizados dentro de. Las traducciones de texto deben introducirse en un `pass.strings` archivo, mientras que las imágenes localizadas deben tener el mismo nombre que la imagen que reemplazan en la raíz del paso.

## <a name="security"></a>Seguridad

Las pasadas se firman con un certificado privado que se genera en el portal de aprovisionamiento de iOS. Los pasos para firmar el paso son:

1. Calcular un hash SHA1 para cada archivo en el directorio de paso (no incluya el `manifest.json` `signature` archivo o, ninguno de los cuales debe existir en esta fase de todos modos).
1. Escriba `manifest.json` como una lista de clave/valor JSON de cada nombre de archivo con su hash.
1. Utilice el certificado para firmar el `manifest.json` archivo y escribir el resultado en un archivo denominado `signature` .
1. COMPRIMIR todo y dar al archivo resultante una extensión de `.pkpass` archivo.

Dado que la clave privada es necesaria para firmar el paso, este proceso solo debe realizarse en un servidor seguro que usted controle. NO distribuya las claves para probar y generar pasadas en una aplicación.

## <a name="configuration-and-setup"></a>Instalación y configuración

Esta sección contiene instrucciones para ayudar a configurar los detalles de aprovisionamiento y crear el primer paso.

### <a name="provisioning-passkit"></a>Aprovisionamiento de PassKit

Para que un paso entre en la tienda de aplicaciones, debe estar vinculado a una cuenta de desarrollador. Esto requiere dos pasos:

1. El paso se debe registrar con un identificador único, denominado identificador de tipo de paso.
1. Se debe generar un certificado válido para firmar el paso con la firma digital del desarrollador.

Para crear un identificador de tipo de paso, haga lo siguiente.

#### <a name="create-a-pass-type-id"></a>Crear un ID. de tipo de paso

El primer paso consiste en configurar un identificador de tipo de paso para que se admita cada _tipo_ de paso diferente. El identificador de paso (o el identificador de tipo de paso) crea un identificador único para el paso. Usaremos este identificador para vincular el paso con la cuenta de desarrollador mediante un certificado.

1. En la [sección certificados, identificadores y perfiles del portal de aprovisionamiento de iOS](https://developer.apple.com/account/overview.action), vaya a **identificadores** y seleccione **pasar identificadores de tipo** . Después, seleccione el **+** botón para crear un nuevo tipo de paso: [ ![ crear un nuevo tipo de paso](passkit-images/passid.png)](passkit-images/passid.png#lightbox)

2. Proporcione una **Descripción** (nombre) e **identificador** (cadena única) para el paso. Tenga en cuenta que todos los identificadores de tipo de paso deben comenzar con la cadena `pass.` en este ejemplo, se usa `pass.com.xamarin.coupon.banana` : [ ![ proporcione una descripción y un identificador](passkit-images/register.png)](passkit-images/register.png#lightbox) .

3. Confirme el ID. de paso presionando el botón **registrar** .

#### <a name="generate-a-certificate"></a>Generar un certificado

Para crear un nuevo certificado para este identificador de tipo de paso, haga lo siguiente:

1. Seleccione el ID. de paso recién creado en la lista y haga clic en **Editar** : [ ![ Seleccione el nuevo ID. de paso de la lista.](passkit-images/pass-done.png)](passkit-images/pass-done.png#lightbox)

    A continuación, seleccione **crear certificado..** . :

    [![Seleccione Crear certificado.](passkit-images/cert-dist.png)](passkit-images/cert-dist.png#lightbox)

2. Siga los pasos para crear una solicitud de firma de certificado (CSR).
  
3. Presione el botón **continuar** en el portal para desarrolladores y cargue el CSR para generar el certificado.

4. Descargue el certificado y haga doble clic en él para instalarlo en la cadena de claves.

Ahora que hemos creado un certificado para este identificador de tipo de paso, en la sección siguiente se describe cómo crear un paso manualmente.

Para obtener más información sobre el aprovisionamiento de Wallet, consulte la guía [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) .

### <a name="create-a-pass-manually"></a>Crear un paso manualmente

Ahora que hemos creado el tipo de paso, podemos crear manualmente un paso para probarlo en el simulador o en un dispositivo. Los pasos para crear un paso son:

- Cree un directorio que contenga los archivos de paso.
- Cree un pass.jsen el archivo que contenga todos los datos necesarios.
- Incluir imágenes en la carpeta (si es necesario).
- Calcule los hashes SHA1 para cada archivo de la carpeta y escriba en manifest.jsen.
- Inicie sesión manifest.jscon el archivo Certificate. p12 descargado.
- COMPRIMIR el contenido del directorio y cambiar el nombre con la extensión. pkpass.

En el [código de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) de este artículo hay algunos archivos de código fuente que se pueden usar para generar un paso. Use los archivos del `CouponBanana.raw` directorio del directorio CreateAPassManually Los archivos siguientes están presentes:

 [![Estos archivos están presentes](passkit-images/image18.png)](passkit-images/image18.png#lightbox)

Abra pass.jsen y edite el archivo JSON. Debe actualizar al menos el `passTypeIdentifier` y `teamIdentifer` para que coincida con su cuenta de desarrollador de Apple.

```json
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

A continuación, debe calcular los valores hash de cada archivo y crear el `manifest.json` archivo. Tendrá un aspecto similar al siguiente cuando haya terminado:

```json
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

Descargue los **materiales de soporte técnico** de la semilla de Wallet desde el sitio de [descargas de Apple](https://developer.apple.com/downloads/index.action?name=Passbook) . Use la `signpass` herramienta para convertir la carpeta en un paso (esto también calculará los hashes SHA1 y comprima la salida en un archivo. pkpass).

#### <a name="testing"></a>Prueba

Si va a examinar la salida de estas herramientas (estableciendo el nombre de archivo en. zip y abriéndolo), verá los siguientes archivos (tenga en cuenta la adición de los `manifest.json` archivos y `signature` ):

 [![Examinar la salida de estas herramientas](passkit-images/image19.png)](passkit-images/image19.png#lightbox)

Una vez que haya firmado, comprimido y cambiado el nombre del archivo (por ejemplo, a `BananaCoupon.pkpass` ) puede arrastrarlo al simulador para probarlo o enviarlo por correo electrónico para que lo recupere en un dispositivo real. Debería ver una pantalla para **Agregar** el paso, de la siguiente manera:

 [![Agregar la pantalla de paso](passkit-images/image20.png)](passkit-images/image20.png#lightbox)

Normalmente, ese proceso se automatizaría en un servidor, pero la creación de paso manual podría ser una opción para las pequeñas empresas que solo crean cupones que no requieren la compatibilidad de un servidor back-end.

## <a name="wallet"></a>Cartera

Wallet es la parte central del ecosistema de PassKit. En esta captura de pantalla se muestra la cartera vacía y la apariencia de la lista de pasadas y las fases individuales:

 [![En esta captura de pantalla se muestra la cartera vacía y el aspecto de la lista de pasadas y las fases individuales.](passkit-images/image21.png)](passkit-images/image21.png#lightbox)

Las características de Wallet incluyen:

- Es el único lugar en el que se representan los pasos con su código de barras para el examen.
- El usuario puede cambiar la configuración de las actualizaciones. Si está habilitada, las notificaciones de extracción pueden desencadenar actualizaciones de los datos en el paso.
- El usuario puede habilitar o deshabilitar la integración de la pantalla de bloqueo. Si está habilitada, permite que el paso aparezca automáticamente en la pantalla de bloqueo, en función de la hora y los datos de ubicación relevantes insertados en el paso.
- El lado inverso de la pasada admite la extracción a la actualización, si se proporciona una dirección URL de Web-Server en el JSON de Pass.
- Las aplicaciones complementarias se pueden abrir (o descargar) si el identificador de la aplicación se proporciona en el JSON de Pass.
- Las pasadas se pueden eliminar (con una animación de fragmentación de un bonito).

## <a name="adding-passes-into-wallet"></a>Agregar pasadas a Wallet

Las pasadas se pueden agregar a Wallet de las siguientes maneras:

- **Aplicaciones de conducto** : estos no manipulan los pasos directamente, simplemente cargan los archivos de paso y presentan al usuario la opción de agregarlos a wallet. 

- **Aplicaciones complementarias** : las escriben los proveedores para distribuir los pasos y ofrecen funcionalidad adicional para examinarlos o editarlos. Las aplicaciones de Xamarin. iOS tienen acceso completo a la API de PassKit para crear y manipular las pasadas. Después, se pueden agregar pasadas a Wallet mediante `PKAddPassesViewController` . Este proceso se describe con más detalle en la sección **aplicaciones complementarias** de este documento.

### <a name="conduit-applications"></a>Aplicaciones de conducto

Las aplicaciones de conducto son aplicaciones intermedias que pueden recibir pasadas en nombre de un usuario y deben programarse para que reconozcan su tipo de contenido y proporcionen la funcionalidad que se va a agregar a wallet. Los ejemplos de aplicaciones de conducto incluyen:

- **Mail** : reconoce los datos adjuntos como un paso.
- **Safari** : reconoce el tipo de contenido de Pass cuando se hace clic en un vínculo de dirección URL de paso.
- **Otras aplicaciones personalizadas** : cualquier aplicación que reciba datos adjuntos o vínculos abiertos (clientes de medios sociales, lectores de correo, etc.).

En esta captura de pantalla se muestra cómo **mail** in iOS 6 reconoce una oferta de datos adjuntos de Pass y (cuando se toca) para **agregarla** a wallet.

 [![En esta captura de pantalla se muestra cómo mail in iOS 6 reconoce un archivo adjunto de Pass](passkit-images/image22.png)](passkit-images/image22.png#lightbox)

 [![En esta captura de pantalla se muestra cómo proporciona correo electrónico para agregar datos adjuntos de Pass a Wallet](passkit-images/image23.png)](passkit-images/image23.png#lightbox)

Si va a compilar una aplicación que podría ser un conducto para las pasadas, pueden ser reconocidas por:

- **Extensión de archivo** :. pkpass
- **Tipo MIME** : aplicación/vnd. Apple. pkpass
- **UTI** : com. Apple. pkpass

La operación básica de una aplicación de conductos es recuperar el archivo de paso y llamar a PassKit `PKAddPassesViewController` para proporcionar al usuario la opción de agregar el paso a su cartera. La implementación de este controlador de vistas se trata en la sección siguiente en **las aplicaciones complementarias**.

No es necesario aprovisionar aplicaciones de conducto para un identificador de tipo de paso específico de la misma manera que lo hacen las aplicaciones complementarias.

## <a name="companion-applications"></a>Aplicaciones complementarias

Una aplicación complementaria proporciona funcionalidad adicional para trabajar con pasadas, incluida la creación de un paso, la actualización de la información asociada a un paso y la administración de las pasadas asociadas a la aplicación.

Las aplicaciones complementarias no deben intentar duplicar las características de wallet. No están diseñados para mostrar las pasadas para el examen.

En el resto de esta sección se describe cómo crear una aplicación complementaria básica que interactúe con PassKit.

### <a name="provisioning"></a>Aprovisionamiento

Dado que Wallet es una tecnología de tienda, la aplicación debe aprovisionarse por separado y no puede usar el perfil de aprovisionamiento de equipo o el identificador de aplicación comodín. Consulte la guía [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) para crear un identificador de aplicación único y un perfil de aprovisionamiento para la aplicación wallet.

### <a name="entitlements"></a>Derechos

El archivo **contitles. plist** debe estar incluido en todos los proyectos de Xamarin. iOS recientes. Para agregar un nuevo archivo contitles. plist, siga los pasos de la guía [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) .

Para establecer los derechos, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Haga doble clic en el archivo **contitles. plist** en el panel de solución para abrir el editor de derechos. plist:

![Contitles. Editor plst](passkit-images/image31.png)

En la sección Wallet, seleccione la opción **Habilitar Wallet** .

![Habilitar derechos de Wallet](passkit-images/image32.png)

La opción predeterminada es que la aplicación permita todos los tipos de pase. Sin embargo, es posible restringir la aplicación y permitir solo un subconjunto de tipos de pases de equipo. Para habilitar esta opción, seleccione el **subconjunto permitir de los tipos de Pass del equipo** y escriba el identificador de tipo de paso del subconjunto que desea permitir.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Haga doble clic en el archivo **contitles. plist** para abrir el archivo de origen XML.

Para agregar el derecho de Wallet, establezca la **propiedad** en `Passbook Identifiers` en la lista desplegable, que establecerá automáticamente el **tipo** `Array` . A continuación, establezca el **valor** de cadena en `$(TeamIdentifierPrefix)*` :

![Habilitar derechos de Wallet](passkit-images/image33.png)

De esta manera, la aplicación admitirá todos los tipos de pases. Para restringir la aplicación y admitir únicamente un subconjunto de tipos de pase de equipo, establezca el valor de la cadena en: 

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Donde `pass.$(CFBundleIdentifier)` es el identificador de paso que se ha creado [anteriormente](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Depuración

Si tiene problemas para implementar la aplicación, compruebe que está usando el **Perfil de aprovisionamiento** correcto y que `Entitlements.plist` está seleccionado como el archivo de **derechos personalizados** en las opciones de **firma de paquete de iPhone** .

Si experimenta este error al implementar:

```
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

después, la `pass-type-identifiers` matriz de derechos es incorrecta (o no coincide con el **Perfil de aprovisionamiento**). Compruebe que los identificadores de tipo de paso y su identificador de equipo son correctos.

## <a name="classes"></a>Clases

Las siguientes clases de PassKit están disponibles para que las aplicaciones tengan acceso a los pasos:

- **PKPass** : una instancia de un paso.
- **PKPassLibrary** : proporciona la API para acceder a las pasadas en el dispositivo.
- **PKAddPassesViewController** : se usa para mostrar un paso que el usuario debe guardar en su cartera.
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

Las aplicaciones pueden solicitar una lista de las pasadas de la biblioteca. Esta lista se filtra automáticamente por PassKit, de modo que solo puede ver las pasadas que se han creado con su identificador de equipo y que se enumeran en los derechos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Tenga en cuenta que el simulador no filtra la lista de las pasadas devueltas, por lo que este método siempre debe probarse en dispositivos reales. Esta lista se puede mostrar en un UITableView. La [aplicación de ejemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) tiene el siguiente aspecto después de haber agregado dos cupones:

 [![La aplicación de ejemplo tiene el siguiente aspecto después de haber agregado dos cupones](passkit-images/image29.png)](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Mostrar pasadas

Hay disponible un conjunto limitado de información para la representación de pasadas dentro de aplicaciones complementarias.

Elija en este conjunto de propiedades estándar para mostrar las listas de pasadas, como hace el código de ejemplo.

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

 [![La alerta de cupón seleccionada en el ejemplo](passkit-images/image30.png)](passkit-images/image30.png#lightbox)

También puede usar el `LocalizedValueForFieldKey()` método para recuperar datos de los campos de las pasadas que ha diseñado (ya que sabrá qué campos deben estar presentes). En el código de ejemplo no se muestra esto.

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

 [![El paso presentado con las opciones Agregar y cancelar](passkit-images/image20.png)](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Reemplazar un paso existente

Si se reemplaza un paso existente, no se requiere el permiso del usuario; sin embargo, se producirá un error si no existe el paso.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Edición de un paso

PKPass no es mutable, por lo que no se pueden actualizar objetos Pass en el código. Para modificar los datos de un paso, una aplicación debe tener acceso a un servidor Web que pueda mantener un registro de pasadas y generar un nuevo archivo de paso con valores actualizados que la aplicación pueda descargar.

La creación de un archivo de paso debe realizarse en un servidor, ya que las pasadas deben estar firmadas con un certificado que se debe mantener de forma privada y segura.

Una vez que se ha generado un archivo de paso actualizado, use el `Replace` método para sobrescribir los datos antiguos en el dispositivo.

### <a name="display-a-pass-for-scanning"></a>Mostrar un paso para el examen

Como se indicó anteriormente, solo Wallet puede mostrar un paso para la digitalización. Se puede mostrar un paso mediante el `OpenUrl` método, como se muestra A continuación:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Recibir notificaciones de cambios

Las aplicaciones pueden escuchar los cambios que se realizan en la biblioteca de paso mediante `PKPassLibraryDidChangeNotification` . Los cambios pueden deberse a que las notificaciones desencadenan actualizaciones en segundo plano, por lo que se recomienda realizar escuchas en la aplicación.

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

Vea [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook) código abierto de C# del servidor de código abierto.

## <a name="push-notifications"></a>Notificaciones de inserción

Una explicación detallada del uso de notificaciones de extracción para actualizar los pasos queda fuera del ámbito de este artículo introductorio.

Sería necesario implementar la API de tipo REST definida por Apple para responder a las solicitudes Web de Wallet cuando se requieran actualizaciones.

Consulte la guía de [actualización de un paso](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) de Apple para obtener más información.

## <a name="summary"></a>Resumen

En este artículo se presentaron PassKit, se detallaron algunas de las razones por las que resulta útil y se describen las diferentes partes que se deben implementar para una solución de PassKit completa. Describe los pasos necesarios para configurar la cuenta de desarrollador de Apple para crear pasadas, el proceso para realizar un paso manualmente y también cómo acceder a las API de PassKit desde una aplicación de Xamarin. iOS.

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
