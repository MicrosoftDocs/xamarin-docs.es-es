---
title: Cambios en StoreKit en iOS 6
description: 'iOS 6 presenta dos cambios en la API del kit de tiendas: la capacidad de mostrar los productos iTunes (y App Store/iBookstore) desde dentro de la aplicación y una nueva opción de compra desde la aplicación en la que Apple hospedará los archivos descargables. En este documento se explica cómo implementar esas características con Xamarin. iOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 7cf18934c70acf59213a697ab57b6c5e308e7b2a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725226"
---
# <a name="changes-to-storekit-in-ios-6"></a>Cambios en StoreKit en iOS 6

_iOS 6 presentó dos cambios en la API del kit de tiendas: la capacidad de mostrar los productos iTunes (y App Store/iBookstore) desde dentro de la aplicación y una nueva opción de compra desde la aplicación en la que Apple hospedará los archivos descargables. En este documento se explica cómo implementar esas características con Xamarin. iOS._

Los principales cambios en el kit de almacenamiento de iOS6 son las dos nuevas características:

- **Visualización del contenido de la aplicación & compras** : los usuarios pueden comprar y descargar aplicaciones, música, libros y otro contenido de iTunes sin tener que cerrar la aplicación. También puede vincular a sus propias aplicaciones para promover la compra o simplemente animar las revisiones y clasificaciones.
- **Contenido hospedado de compras desde la aplicación** : Apple almacenará y entregará el contenido asociado a los productos de compras desde la aplicación, lo que elimina la necesidad de que un servidor independiente hospede los archivos, admite automáticamente la descarga en segundo plano y permite escribir menos código.

Consulte las guías de [compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md) para obtener una explicación detallada de las API de StoreKit.

## <a name="requirements"></a>Requisitos

Las características del kit de almacén que se describen en este documento requieren iOS 6 y Xcode 4,5, junto con Xamarin. iOS 6,0.

## <a name="in-app-content-display--purchasing"></a>Visualización del contenido de la aplicación & Purchasing

La nueva característica de compra desde la aplicación en iOS permite a los usuarios ver la información del producto y comprar o descargar el producto desde dentro de la aplicación.
Anteriormente, las aplicaciones tendrían que desencadenar iTunes, App Store o iBookstore, lo que daría lugar a que el usuario abandonase la aplicación original. Esta nueva característica devolverá automáticamente al usuario a la aplicación cuando haya terminado.

[![](changes-to-storekit-images/image1.png "Automatically returning to an app after purchase")](changes-to-storekit-images/image1.png#lightbox)

Estos son algunos ejemplos de cómo se podría usar:

- **Animar a los usuarios para que califiquen la aplicación** : puede abrir la página de App Store para que el usuario pueda clasificar y revisar la aplicación sin cerrarla.
- **Aplicaciones de promoción cruzada** : permite al usuario ver otras aplicaciones que publique, con la capacidad de comprar o descargar inmediatamente.
- **Ayudar a los usuarios a encontrar y descargar contenido** : Ayude a los usuarios a comprar contenido que la aplicación encuentra, administra o agrega (por ejemplo, una aplicación relacionada con música podría proporcionar una lista de reproducción de canciones y permitir la compra de cada canción dentro de la aplicación).

Una vez que se muestra el `SKStoreProductViewController`, el usuario puede interactuar con la información del producto como si estuviera en iTunes, App Store o iBookstore. El usuario puede:

- Ver capturas de pantallas (para aplicaciones)
- Canciones de ejemplo o vídeo (para música, programas de TV y películas)
- Leer (y escribir) revisiones,
- Compre & descarga, que sucede completamente en el controlador de vista y el kit de tiendas.

Algunas opciones del `SKStoreProductViewController` seguirán forzando al usuario a salir de la aplicación y abrir la aplicación de la tienda pertinente, como hacer clic en **productos relacionados** o en el vínculo de **soporte técnico** de una aplicación.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

La API para mostrar un producto en cualquier aplicación es simple: solo requiere que cree y muestre una `SKStoreProductViewController`. Siga estos pasos para crear y mostrar un producto:

1. Cree un objeto `StoreProductParameters` para pasar parámetros al controlador de vista, incluido el `productId` en el constructor.
1. Cree una instancia del `SKProductViewController`. Asígnelo a un campo de nivel de clase.
1. Asigne un controlador al evento `Finished` del controlador de vista, que debe descartar el controlador de vista. Se llama a este evento cuando el usuario presiona Cancelar; de lo contrario, finaliza una transacción dentro del controlador de vista.
1. Llame al método `LoadProduct` pasando el `StoreProductParameters` y un controlador de finalización. El controlador de finalización debe comprobar que la solicitud de producto se ha realizado correctamente y, en caso afirmativo, presentar el `SKProductViewController` de forma modal. Se debe agregar un control de errores adecuado en caso de que no se pueda recuperar el producto.

### <a name="example"></a>Ejemplo

El proyecto *ProductView* del código de ejemplo *StoreKit* de este artículo implementa un método `Buy` que acepta cualquier identificador de Apple del producto y muestra el `SKStoreProductViewController`. En el código siguiente se muestra la información del producto para cualquier ID. de Apple dado:

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

La aplicación es similar a la captura de pantalla siguiente cuando se ejecuta: la descarga o la compra se produce completamente en el `SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "The app looks like this when running")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Compatibilidad con sistemas operativos anteriores

La aplicación de ejemplo incluye código que muestra cómo abrir App Store, iTunes o iBookstore en versiones anteriores de iOS. Use el método `OpenUrl` para abrir una dirección URL de **iTunes.com** diseñada correctamente.

Puede implementar una comprobación de versión para determinar qué código ejecutar, como se muestra aquí:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>Errors

El siguiente error se producirá si el ID. de Apple que usa no es válido, lo que puede resultar confuso, ya que implica un problema de red o de autenticación de algún tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Lectura de la documentación de Objective-C

Los desarrolladores que lean sobre el kit de tiendas en el portal para desarrolladores de Apple verán un protocolo – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) , que se describe en relación con esta nueva característica. El protocolo delegado tiene solo un método: productViewControllerDidFinish, que se ha expuesto como el evento `Finished` en el `SKStoreProductViewController` en Xamarin. iOS.

## <a name="determining-apple-ids"></a>Determinación de los identificadores de Apple

El identificador de Apple requerido por la `SKStoreProductViewController` es un *número* (no debe confundirse con los identificadores de agrupación como "com. Xamarin. MWC2012"). Hay varias maneras de averiguar el ID. de Apple para los productos que desea mostrar, que se enumeran a continuación:

### <a name="itunesconnect"></a>iTunesConnect

En el caso de las aplicaciones que publique, es fácil encontrar el **identificador de Apple** en iTunes Connect:

[![](changes-to-storekit-images/image3.png "Finding the Apple ID in iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API de búsqueda

Apple proporciona una API de búsqueda dinámica para consultar todos los productos de la tienda de aplicaciones, iTunes y iBookstore. Puede encontrar información sobre cómo acceder a la API de búsqueda en recursos afiliados de Apple, aunque la API se expone a cualquier persona (no solo a las filiales registradas). Se puede analizar el JSON resultante para detectar el `trackId` que es el identificador de Apple que se va a usar con `SKStoreProductViewController`.

Los resultados también incluirán otros metadatos, como información de visualización y direcciones URL de material gráfico que se pueden usar para representar el producto en la aplicación.

Estos son algunos ejemplos:

- **aplicación iBooks** : [https://itunes.apple.com/search?term=ibooks&amp; entidad = software&amp;Country = US](https://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **DOT y Kangaroo iBooks** – [https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; entity = ebook&amp;Country = US](https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Fuente de socios empresariales

Apple proporciona a los asociados aprobados un volcado de datos completo de todos sus productos, en forma de archivos planos listos para la base de datos que se pueden descargar. Si califica para el acceso a la fuente de socios empresariales, el ID. de Apple de cualquier producto puede encontrarse en ese conjunto de certificados.

Muchos usuarios de la fuente de socios empresariales son miembros del [programa afiliados](https://www.apple.com/itunes/affiliates) que permite que las comisiones se acumulen en las ventas de productos. `SKStoreProductViewController` no admite identificadores afiliados (en el momento de la escritura).

### <a name="direct-product-links"></a>Vínculos de productos directos

El ID. de Apple de un producto se puede inferir de su vínculo de dirección URL de iTunes Preview.
En cualquier vínculo de producto de iTunes (para aplicaciones, música o libros), busque la parte de la dirección URL que empieza por `id` y use el número que aparece a continuación.

Por ejemplo, el vínculo directo a iBooks es

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

y el ID. de Apple es **364709193**. De forma similar a la aplicación MWC2012, el vínculo directo es

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

y el ID. de Apple es **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Contenido hospedado de compras desde la aplicación

Si las compras desde la aplicación están compuestas por contenido descargable (por ejemplo, libros u otros medios, material gráfico y configuración de nivel de juego u otros archivos grandes), estos archivos se usan para hospedarse en el servidor Web y las aplicaciones tenían que incorporar código para descargarlos de forma segura después de ventas. A partir de iOS 6, Apple hospedará sus archivos en sus servidores, lo que elimina la necesidad de un servidor independiente. La característica solo está disponible para productos no consumibles (no consumibles o suscripciones). Entre las ventajas de usar el servicio de hospedaje de Apple se incluyen:

- Ahorre costos de ancho de banda &.
- Probablemente más escalable que el host de servidor que está usando actualmente.
- Menos código que escribir, ya que no tiene que compilar ningún procesamiento en el lado servidor.
- La descarga en segundo plano se implementa automáticamente.

Nota: no se admite la prueba de contenido de compra desde la aplicación en el simulador de iOS, por lo que debe realizar pruebas con un dispositivo real.

### <a name="hosted-content-basics"></a>Conceptos básicos de contenido hospedado

Antes de iOS 6, había dos maneras de proporcionar un producto (se describe con más detalle en [la documentación de compras desde la aplicación de Xamarin](~/ios/platform/in-app-purchasing/index.md) ):

- **Productos integrados** : características que se desbloquean mediante la compra, pero que están integradas en la aplicación (ya sea como código o recursos incrustados). Entre los ejemplos de productos integrados se incluyen los filtros de foto desbloqueados o las potencias de juego.
- **Productos entregados por el servidor** : después de la compra, la aplicación debe descargar contenido de un servidor que opere. Este contenido se descarga durante la compra, se almacena en el dispositivo y, a continuación, se representa como parte del suministro del producto. Entre los ejemplos se incluyen libros, problemas de revistas o niveles de juego que se componen de archivos de imagen y de configuración.

En iOS 6 Apple ofrece una variación de los productos proporcionados por el servidor: hospedará los archivos de contenido en sus servidores. Esto hace que sea mucho más sencillo compilar productos entregados por el servidor porque no es necesario que opere en un servidor independiente, y el kit de almacén proporciona una funcionalidad de descarga en segundo plano que anteriormente tenía que escribir. Para aprovechar el hospedaje de Apple, habilite el hospedaje de contenido para los nuevos productos de compras desde la aplicación y modifique el código del kit de tiendas para aprovecharlo. Los archivos de contenido del producto se compilan con Xcode y se cargan en los servidores de Apple para su revisión y publicación.

[![](changes-to-storekit-images/image4.png "The build and deliver process")](changes-to-storekit-images/image4.png#lightbox)

El uso de la tienda de aplicaciones para proporcionar compras desde la aplicación *con contenido hospedado* requiere la siguiente configuración y configuración:

- **iTunes Connect** : *debe* haber proporcionado su información bancaria y fiscal a Apple para que puedan remitir los fondos recopilados en su nombre. Después, puede configurar los productos para vender y configurar cuentas de usuario de espacio aislado para probar la compra.  _También debe configurar el contenido hospedado para los productos que no se pueden consumir y que desea hospedar con Apple_.
- **portal de aprovisionamiento de iOS** : creación de un identificador de paquete y habilitación del acceso al almacén de aplicaciones para la aplicación, como haría con cualquier aplicación que admita la compra desde la aplicación.
- **Kit de tienda** : agregar código a la aplicación para mostrar productos, comprar productos y restaurar transacciones.  _En el kit de tiendas de iOS 6 también se administrará la descarga del contenido del producto, en segundo plano, con las actualizaciones de progreso._
- **Código personalizado** : para realizar el seguimiento de las compras realizadas por los clientes y proporcionar los productos o servicios que han adquirido. Use las nuevas clases del kit de almacenamiento de iOS 6 como `SKDownload` para recuperar el contenido hospedado por Apple.

En las secciones siguientes se explica cómo implementar el contenido hospedado, desde la creación y carga del paquete hasta la administración del proceso de compra y descarga, utilizando el código de ejemplo de este artículo.

### <a name="sample-code"></a>Código de ejemplo

El proyecto de ejemplo *HostedNonConsumables* (en StoreKitiOS6. zip) usa contenido hospedado. La aplicación ofrece dos "capítulos del libro" para su venta, el contenido que se hospeda en los servidores de Apple. El contenido se compone de un archivo de texto y una imagen, aunque se podría usar contenido mucho más complejo en una aplicación real.

La aplicación tiene este aspecto antes, durante y después de una compra:

 [![](changes-to-storekit-images/image5.png "The app looks like this before, during and after a purchase")](changes-to-storekit-images/image5.png#lightbox)

El archivo de texto y la imagen se descargan y copian en el directorio de documentos de la aplicación. Para obtener más información sobre los distintos directorios disponibles para el almacenamiento de aplicaciones, consulte la [documentación del sistema de archivos](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Al crear nuevos productos que usarán el hospedaje de contenido de Apple, asegúrese de seleccionar el tipo de producto **no consumible** . Otros tipos de productos no admiten el hospedaje de contenido. Además, no debe habilitar el hospedaje de contenido *para los productos* que vende; Active únicamente el hospedaje de contenido para los nuevos productos.

 [![](changes-to-storekit-images/image6.png "Select the Non-Consumable product type")](changes-to-storekit-images/image6.png#lightbox)

Escriba un **ID**. de producto. Este ID. se requerirá más adelante cuando cree el contenido de este producto.

 [![](changes-to-storekit-images/image7.png "Enter a Product ID")](changes-to-storekit-images/image7.png#lightbox)

El hospedaje de contenido se establece en la sección detalles. Antes de que se activara la compra desde la aplicación, desactive la casilla **contenido del host con Apple** si quiere cancelar (incluso si ha cargado algún contenido de prueba). Sin embargo, no se puede quitar el hospedaje de contenido después de que la compra desde la aplicación haya dejado de funcionar.

 [![](changes-to-storekit-images/image8.png "Hosting content with Apple")](changes-to-storekit-images/image8.png#lightbox)

Una vez que haya activado el contenido de hospedaje, el producto entrará en **espera del estado de carga** y mostrará este mensaje:

 [![](changes-to-storekit-images/image9.png "The product will enter Waiting for Upload status and show this message")](changes-to-storekit-images/image9.png#lightbox)

El paquete de contenido debe crearse con Xcode y cargarse mediante la herramienta de archivo. Las instrucciones para crear paquetes de contenido se proporcionan en la sección siguiente **creación de. Archivos PKG**.

## <a name="creating-pkg-files"></a>Crean. Archivos PKG

Los archivos de contenido que se cargan en Apple deben cumplir las restricciones siguientes:

- No puede tener un tamaño superior a 2 GB.
- No puede contener código ejecutable (o vínculos simbólicos que apunten fuera del contenido).
- Debe tener el formato correcto (incluido un archivo **. plist** ) y tener una extensión de archivo **. pkg** . Esto se realizará automáticamente si sigue estas instrucciones con Xcode.

Puede agregar muchos archivos y tipos de archivos diferentes, siempre y cuando cumplan estas restricciones. El contenido se comprime antes de la entrega a la aplicación y se descomprime por el kit de almacén antes de que el código tenga acceso a él.

Después de cargar un paquete de contenido, se puede reemplazar por el contenido más reciente. El nuevo contenido debe cargarse y enviarse para su revisión y aprobación a través del proceso normal. Incremente el campo `ContentVersion` en paquetes de contenido actualizado para indicar que es más reciente.

### <a name="xcode-in-app-purchase-content-projects"></a>Proyectos de contenido de compra desde la aplicación de Xcode

La creación de paquetes de contenido para productos de compra desde la aplicación actualmente requiere Xcode. No se requiere ningún código de OBJECTIVE-C. Xcode tiene un nuevo tipo de proyecto para estos paquetes que solo contiene los archivos y un archivo plist.

Nuestra aplicación de ejemplo tiene capítulos de la venta: cada paquete de contenido de capítulos contendrá:

- un archivo de texto y
- una imagen para representar el capítulo.

Para empezar, seleccione **archivo > nuevo proyecto** en el menú y elija **contenido de compra**desde la aplicación:

 [![](changes-to-storekit-images/image10.png "Choose In-App Purchase Content")](changes-to-storekit-images/image10.png#lightbox)

Escriba el **nombre del producto** y el **identificador** de la empresa de forma que el **identificador del paquete** coincida con el ID. de **producto** especificado en iTunes Connect para este producto.

[![](changes-to-storekit-images/image11.png "Enter the  Name and Identifier")](changes-to-storekit-images/image11.png#lightbox)

Ahora tendrá un proyecto **de contenido de compra en la aplicación en** blanco. Puede hacer clic con el botón derecho y **Agregar archivos...** o bien, arrástrelos al **navegador del proyecto**. Asegúrese de que **ContentVersion** sea correcto (debe comenzar en 1,0, pero si más adelante decide actualizar el contenido, no olvide incrementarlo).

En esta captura de pantalla se muestra Xcode con los archivos de contenido incluidos en el proyecto y las entradas plist visibles en la ventana principal:

[![](changes-to-storekit-images/image12.png "This screenshot shows Xcode with the content files included in the project and the plist entries visible in the main window")](changes-to-storekit-images/image12.png#lightbox)

Una vez que haya agregado todos los archivos de contenido, puede guardar este proyecto y editarlo de nuevo más adelante, o iniciar el proceso de carga.

## <a name="uploading-pkg-files"></a>Cargar. Archivos PKG

La forma más sencilla de cargar paquetes de contenido es con la **herramienta de archivo Xcode**. Elija **Product > Archive** en el menú para empezar:

![](changes-to-storekit-images/image13.png "Choose Archiven")

El paquete de contenido aparecerá en el archivo tal y como se muestra a continuación.
El tipo de archivo y el icono muestran esta línea es un **archivo de contenido de compra desde la aplicación**. Haga clic en **validar...** para comprobar si hay errores en el paquete de contenido sin realizar realmente la carga.

[![](changes-to-storekit-images/image14.png "Validate the package")](changes-to-storekit-images/image14.png#lightbox)

Inicie sesión con sus credenciales de iTunes Connect:

[![](changes-to-storekit-images/image15.png "Login with your iTunes Connect credentials")](changes-to-storekit-images/image15.png#lightbox)

Elija la aplicación correcta y la compra desde la aplicación para asociar este contenido a:

[![](changes-to-storekit-images/image16.png "Choose the correct application and in-app purchase to associate this content with")](changes-to-storekit-images/image16.png#lightbox)

Debería ver un mensaje similar a esta captura de pantalla:

![Un mensaje de ejemplo sin problemas](changes-to-storekit-images/image17.png "Un mensaje de ejemplo sin problemas")

Ahora siga un proceso similar, pero haciendo clic en **distribuir...** cargará realmente el contenido.

[![Distribuir la aplicación](changes-to-storekit-images/image18.png "Distribuir la aplicación")](changes-to-storekit-images/image18.png#lightbox)

Seleccione la primera opción para cargar el contenido:

![Cargar el contenido](changes-to-storekit-images/image19.png "Cargar el contenido")

Vuelva a iniciar sesión:

[![](changes-to-storekit-images/image15.png "Login in")](changes-to-storekit-images/image15.png#lightbox)

Elija la aplicación correcta y el registro de compras desde la aplicación para cargar el contenido en:

[![](changes-to-storekit-images/image20.png "Choose the application and in-app purchase record")](changes-to-storekit-images/image20.png#lightbox)

Espere mientras se cargan los archivos:

[![](changes-to-storekit-images/image21.png "The content upload dialog")](changes-to-storekit-images/image21.png#lightbox)

Una vez completada la carga, aparecerá un mensaje que le indicará que el contenido se ha enviado a la tienda de aplicaciones.

[![](changes-to-storekit-images/image22.png "An example successful upload message")](changes-to-storekit-images/image22.png#lightbox)

Una vez hecho esto, cuando regrese a la página del producto en iTunes Connect, se mostrarán los detalles del paquete y estarán **listos para enviar** el estado. Cuando el producto se encuentra en este estado, puede iniciar las pruebas en el entorno de espacio aislado. NO es necesario "enviar" el producto para realizar pruebas en el espacio aislado.

[![](changes-to-storekit-images/image23.png "iTunes Connect it will show the package details and be in Ready to Submit status")](changes-to-storekit-images/image23.png#lightbox)

Puede tardar algún tiempo (por ejemplo, unos minutos) entre la carga del archivo y el estado de iTunes Connect que se está actualizando. Puede enviar el producto para su revisión por separado o enviarlo junto con un archivo binario de la aplicación. Solo después de que Apple haya aprobado oficialmente el contenido, estará disponible en la tienda de aplicaciones de producción para su compra en la aplicación.

### <a name="pkg-file-format"></a>Formato de archivo PKG

Usar Xcode y la herramienta de archivo para crear y cargar un paquete de contenido hospedado significa que nunca verá el contenido del paquete en sí. Los archivos y directorios de los paquetes creados para la aplicación de ejemplo se asemejan a la captura de pantalla siguiente, con el archivo **plist** en los archivos raíz y de producto en un subdirectorio de **contenido** :

[![](changes-to-storekit-images/image24.png "The plist file in the root and the product files in a Contents subdirectory")](changes-to-storekit-images/image24.png#lightbox)

Tenga en cuenta la estructura de directorios del paquete (especialmente la ubicación de los archivos en el subdirectorio `Contents`) porque deberá comprender esta información para extraer los archivos del paquete en el dispositivo.

### <a name="updating-package-content"></a>Actualizando el contenido del paquete

El procedimiento para actualizar el contenido después de que se haya aprobado:

- Edite el proyecto de contenido de compra desde la aplicación en Xcode.
- Subir el número de versión.
- Vuelva a cargar a iTunes Connect. Los compradores posteriores recibirán automáticamente la versión más reciente, pero los usuarios que ya tengan la versión anterior no recibirán ninguna notificación.
- La aplicación es responsable de notificar a los usuarios y animarles a que recuperen una versión más reciente del contenido. La aplicación también debe compilar una función que descargue la nueva versión mediante la característica de restauración del kit de tiendas.
- Para determinar si existe una versión más reciente, puede compilar una característica en la aplicación para obtener SKProducts (p. ej., el mismo proceso que se usa para recuperar los precios de los productos) y comparar la propiedad ContentVersion.

## <a name="purchasing-overview"></a>Información general sobre compras

Antes de leer esta sección, revise la [documentación de compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)existente.

La secuencia de eventos que se produce cuando se adquiere un producto con contenido hospedado y la descarga se muestra en este diagrama:

[![](changes-to-storekit-images/image25.png "The sequence of events that occurs when a product with hosted content is purchased and download")](changes-to-storekit-images/image25.png#lightbox)

1. Se pueden crear nuevos productos en iTunes Connect con contenido hospedado habilitado. El contenido real se construye por separado en Xcode (simplemente como arrastrar archivos a una carpeta) y, a continuación, se archiva y carga en iTunes (no se requiere ninguna codificación). Después, cada producto se envía para su aprobación, después de que está disponible para su compra. En el código de ejemplo, estos identificadores de producto están codificados, pero el hospedaje de contenido con Apple es más flexible Si almacena la lista de productos disponibles en un servidor remoto para que se pueda actualizar al enviar nuevos productos y contenido a iTunes Connect.
1. Cuando el usuario compra un producto, se coloca una transacción en la cola de pago para su procesamiento.
1. El kit de tiendas reenvía la solicitud de compra a los servidores de iTunes para su procesamiento.
1. La transacción se ha completado en los servidores iTunes (por ejemplo, se cobra al cliente) y se devuelve una confirmación a la aplicación, con la información del producto adjunta, incluido si se pueden descargar (y si es así, el tamaño del archivo y otros metadatos).
1. El código debe comprobar si el producto se puede descargar y, si es así, realizar una solicitud de descarga de contenido que también se coloque en la cola de pago. El kit de tiendas envía esta solicitud a los servidores de iTunes.
1. El servidor devuelve el archivo de contenido al kit de almacenamiento, que proporciona una devolución de llamada para devolver el progreso de la descarga y las estimaciones restantes del código.
1. Una vez que haya finalizado, recibirá una notificación y se le pasará una ubicación de archivo en la carpeta de caché.
1. El código debe copiar los archivos y comprobarlos, guardar cualquier Estado que tenga que recordar que el producto se ha adquirido. Aproveche esta oportunidad para establecer la marca de copia de seguridad correctamente en los nuevos archivos (sugerencia: si proceden de un servidor y nunca los edita el usuario, es probable que omita la copia de seguridad, ya que el usuario siempre puede recuperarlos de los servidores de Apple en el futuro).
1. Llame a FinishTransaction. Este paso es importante, ya que quita la transacción de la cola de pagos. También es importante que no llame a FinishTransaction hasta que haya copiado el contenido fuera del directorio de caché. Una vez que se llama a FinishTransaction, es probable que los archivos almacenados en caché se purguen rápidamente.

## <a name="implementing-hosted-content-purchase"></a>Implementación de la compra de contenido hospedado

La siguiente información se debe leer junto con la documentación completa [de compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md). La información de este documento se centra en las diferencias entre el contenido hospedado y la implementación anterior.

### <a name="classes"></a>Clases

Las siguientes clases se han agregado o modificado para admitir el contenido hospedado en iOS 6:

- **SKDownload** : nueva clase que representa una descarga en curso. La API permite más de una por producto, pero inicialmente solo se ha implementado una.
- **SKProduct** : nuevas propiedades agregadas: `Downloadable`, `ContentVersion`, `ContentLengths` matriz.
- **SKPaymentTransaction** – nueva propiedad agregada: `Downloads`, que contiene una colección de objetos `SKDownload` si este producto tiene contenido hospedado disponible para su descarga.
- **SKPaymentQueue** – nuevo método agregado: `StartDownloads`. Llame a este método con objetos `SKDownload` para capturar su contenido hospedado. La descarga puede producirse en segundo plano.
- **SKPaymentTransactionObserver** – nuevo método: `UpdateDownloads`. El kit de tiendas llama a este método con información de progreso sobre las operaciones de descarga actuales.

Detalles de la nueva clase de `SKDownload`:

- **Progreso** : un valor entre 0-1 que se puede usar para mostrar un indicador de porcentaje completado al usuario. No use Progress = = 1 para detectar si la descarga se ha completado, compruebe que State = = finished.
- **TimeRemaining** : estimación del tiempo de descarga restante, en segundos. -1 significa que todavía está calculando el cálculo.
- **Estado** : activo, en espera, finalizado, error, en pausa, cancelado.
- **ContentURL** : Ubicación del archivo donde se colocó el contenido en el disco, en el directorio de `Cache`. SOLO se rellena una vez finalizada la descarga.
- **Error** : Active esta propiedad si el estado es failed.

Las interacciones entre las clases del código de ejemplo se muestran en este diagrama (el código específico de las compras de contenido hospedado se muestra en verde):

[![](changes-to-storekit-images/image26.png "Hosted content purchases is shown in green in this diagram")](changes-to-storekit-images/image26.png#lightbox)

En el resto de esta sección se muestra el código de ejemplo en el que se han usado estas clases:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Cambie la invalidación de la `UpdatedTransactions` existente para comprobar el contenido descargable y llame a `StartDownloads` si es necesario:

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

A continuación se muestra un nuevo método invalidado `UpdatedDownloads`. El kit de tiendas llama a este método después de que se desencadene `StartDownloads` en `UpdatedTransactions`. Se llama a este método *varias veces* a intervalos indeterminados para proporcionarle el progreso de la descarga y, después, de nuevo cuando finaliza la descarga. Observe que el método acepta una matriz de objetos `SKDownload`, por lo que cada llamada al método puede proporcionar el estado de varias descargas en la cola. Como se muestra en la implementación, los Estados de descarga se comprueban cada vez y se realiza la acción adecuada.

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

Esta clase contiene un nuevo método `SaveDownload` al que se llama después de que se complete correctamente cada descarga.

El contenido hospedado se ha descargado correctamente y se ha descomprimido en el directorio de `Cache`. La estructura de. El archivo PKG requiere que todos los archivos se guarden en un subdirectorio de `Contents`, por lo que el código siguiente extrae los archivos del subdirectorio `Contents`.

El código recorre en iteración todos los archivos del paquete de contenido y los copia en el directorio `Documents`, en una subcarpeta denominada para el `ProductIdentifier`. Finalmente, llama a `CompleteTransaction`, que llama a `FinishTransaction` para quitar la transacción de la cola de pago.

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

Cuando se llama a `FinishTransaction`, ya no se garantiza que los archivos descargados estén en el directorio `Cache`. Se deben copiar todos los archivos antes de llamar a `FinishTransaction`.

## <a name="other-considerations"></a>Otras consideraciones

El código de ejemplo anterior muestra una implementación bastante sencilla de la adquisición de contenido hospedado. Hay algunos puntos adicionales que debe tener en cuenta:

### <a name="detecting-updated-content"></a>Detección de contenido actualizado

Aunque es posible actualizar los paquetes de contenido hospedado, el kit de almacén no proporciona ningún mecanismo para insertar estas actualizaciones en los usuarios que ya han descargado y comprado el producto. Para implementar esta funcionalidad, el código puede comprobar la nueva propiedad `SKProduct.ContentVersion` (si el `SKProduct` se `Downloadable`) con regularidad y detectar si el valor se incrementa. También puede crear un sistema de notificaciones de inserciones.

### <a name="installing-updated-content-versions"></a>Instalación de versiones de contenido actualizadas

El código de ejemplo anterior omite la copia de archivos si el archivo ya existe. NO es una buena idea si desea admitir las versiones más recientes del contenido que se está descargando.

Una alternativa podría ser copiar el contenido en una carpeta con el nombre de la versión y realizar un seguimiento de cuál es la versión actual (por ejemplo, en `NSUserDefaults` o donde almacene los registros de compra completados).

### <a name="restoring-transactions"></a>Restaurar transacciones

Cuando se llama a `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions`, el kit de almacenamiento devuelve todas las transacciones anteriores para el usuario. Si han adquirido un gran número de elementos, o si cada compra tiene paquetes de contenido de gran tamaño, la restauración podría producir una gran cantidad de tráfico de red, ya que todo se pone en cola para su descarga a la vez.

Considere la posibilidad de realizar un seguimiento de si un producto se ha adquirido por separado de la descarga real del paquete de contenido asociado.

### <a name="pausing-restarting-and-canceling-downloads"></a>Pausar, reiniciar y cancelar descargas

Aunque el código de ejemplo no muestra esta característica, es posible pausar y reiniciar descargas de contenido hospedado. El `SKPaymentQueue.DefaultQueue` tiene métodos para `PauseDownloads`, `ResumeDownloads` y `CancelDownloads`.

Si el código llama a `FinishTransaction` en la cola de pago antes de que se `Finished` la descarga, se cancela automáticamente la descarga.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Establecer la marca SKIP-backup en el contenido descargado

Las instrucciones de copia de seguridad de iCloud de Apple sugieren que *no* se debe realizar una copia de seguridad del contenido que no sea de usuario y que se restaure fácilmente desde un servidor (porque innecesariamente el almacenamiento de icloud). Para obtener más información sobre cómo establecer el atributo de copia de seguridad, consulte la documentación [del sistema de archivos](~/ios/app-fundamentals/file-system.md) .

## <a name="summary"></a>Resumen

En este artículo se han incorporado dos nuevas características del kit de tienda en iOS6: compra de iTunes y otro contenido desde dentro de la aplicación, y uso del servidor de Apple para hospedar sus propias compras desde la aplicación. Esta introducción se debe leer junto con la [documentación de compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md) existente para obtener una cobertura completa de la implementación de la funcionalidad del kit de almacenamiento.

## <a name="related-links"></a>Vínculos relacionados

- [StoreKit (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)
- [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)
- [Referencia de StoreKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Referencia de la clase SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Vídeo de WWDC: venta de productos con el kit de tiendas](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
