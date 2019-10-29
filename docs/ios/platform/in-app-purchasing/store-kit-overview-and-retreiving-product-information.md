---
title: Información general de StoreKit y recuperación de la información de producto en Xamarin. iOS
description: En este documento se proporciona información general de StoreKit. Describe las clases usadas con StoreKit, la prueba de interacciones de StoreKit, la visualización de productos de venta, el control de productos no válidos y la visualización de precios localizados.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 086dcb87f03ed4abbf3b82dc10add0f5698d52b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032331"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Información general de StoreKit y recuperación de la información de producto en Xamarin. iOS

La interfaz de usuario para una compra desde la aplicación se muestra en las capturas de pantallas siguientes.
Antes de que tenga lugar cualquier transacción, la aplicación debe recuperar el precio y la descripción del producto para su presentación. Después, cuando el usuario presiona **comprar**, la aplicación realiza una solicitud a StoreKit, que administra el cuadro de diálogo de confirmación y el inicio de sesión de ID de Apple. Suponiendo que la transacción se realiza correctamente, StoreKit notifica al código de la aplicación, que debe almacenar el resultado de la transacción y proporcionar al usuario acceso a su compra.   

 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifies the application code, which must store the transaction result and provide the user with access to their purchase")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Clases

La implementación de compras desde la aplicación requiere las siguientes clases de StoreKit Framework:   
   
 **SKProductsRequest** : una solicitud a StoreKit para productos aprobados para vender (App Store). Se puede configurar con varios identificadores de producto.

- **SKProductsRequestDelegate** : declara métodos para controlar las solicitudes y respuestas del producto. 
- **SKProductsResponse** : se devuelve al delegado desde StoreKit (App Store). Contiene el SKProducts que coincide con los identificadores de producto enviados con la solicitud. 
- **SKProduct** : un producto recuperado de StoreKit (que ha configurado en iTunes Connect). Contiene información sobre el producto, como el ID. del producto, el título, la descripción y el precio. 
- **SKPayment** : se crea con un identificador de producto y se agrega a la cola de pagos para realizar una compra. 
- **SKPaymentQueue** : solicitudes de pago en cola que se enviarán a Apple. Las notificaciones se desencadenan como consecuencia de cada pago que se está procesando. 
- **SKPaymentTransaction** : representa una transacción completa (una solicitud de compra que el App Store ha procesado y que se ha enviado a la aplicación a través de StoreKit). La transacción se puede comprar, restaurar o producir un error. 
- **SKPaymentTransactionObserver** : subclase personalizada que responde a los eventos generados por la cola de pago de StoreKit. 
- **Las operaciones de StoreKit son asincrónicas** : después de iniciar un SKProductRequest o de agregar un SKPayment a la cola, se devuelve el control al código. StoreKit llamará a los métodos de la subclase SKProductsRequestDelegate o SKPaymentTransactionObserver cuando reciba datos de los servidores de Apple. 

En el diagrama siguiente se muestran las relaciones entre las distintas clases StoreKit (las clases abstractas se deben implementar en la aplicación):   

 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "The relationships between the various StoreKit classes abstract classes must be implemented in the app")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   

Estas clases se explican con más detalle más adelante en este documento.

## <a name="testing"></a>Pruebas

La mayoría de las operaciones de StoreKit requieren un dispositivo real para realizar pruebas. Recuperar información del producto (por ej. Price &amp; Description) funcionará en el simulador, pero las operaciones de compra y restauración devolverán un error (por ejemplo, código FailedTransaction = 5002 que se ha producido un error desconocido).

Nota: StoreKit no funciona en el simulador de iOS. Al ejecutar la aplicación en el simulador de iOS, StoreKit registra una advertencia si la aplicación intenta recuperar la cola de pago. La prueba de la tienda debe realizarse en los dispositivos reales.   

Importante: no inicie sesión con su cuenta de prueba en la aplicación de configuración. Puede usar la aplicación de configuración para cerrar la sesión de cualquier cuenta de ID. de Apple existente y, después, debe esperar a que se le solicite en *la secuencia de compra desde la aplicación* para iniciar sesión con un ID. de Apple de prueba.   

Si intenta iniciar sesión en el almacén real con una cuenta de prueba, se convertirá automáticamente a un ID. de Apple real. Esa cuenta ya no se podrá usar para realizar pruebas.

Para probar el código de StoreKit debe cerrar la sesión de la cuenta de prueba de iTunes normal e iniciar sesión con una cuenta de prueba especial (creada en iTunes Connect) que está vinculada al almacén de pruebas. Para cerrar la sesión de la cuenta actual, visite **configuración > iTunes y App Store** , como se muestra aquí:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "To sign out of the current account visit Settings iTunes and App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)

después, inicie sesión con una cuenta de prueba *cuando lo solicite StoreKit dentro de la aplicación*:

Para crear usuarios de prueba en iTunes Connect, haga clic en **usuarios y roles** en la Página principal.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "To create test users in iTunes Connect click on Users and Roles on the main page")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Seleccionar **evaluadores de espacio aislado**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selecting Sandbox Testers")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Se muestra la lista de usuarios existentes. Puede Agregar un nuevo usuario o eliminar un registro existente. El portal no (actualmente) le permite ver o editar los usuarios de prueba existentes, por lo que se recomienda que mantenga un buen registro de cada usuario de prueba que se cree (especialmente la contraseña que asigne). Una vez que se elimina un usuario de prueba, la dirección de correo electrónico no se puede volver a usar para otra cuenta de prueba.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "The list of existing users is displayed")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Los nuevos usuarios de prueba tienen atributos similares a un ID. de Apple real (como el nombre, la contraseña, la pregunta secreta y la respuesta). Mantenga un registro de todos los detalles especificados aquí. El campo **Select iTunes Store** determinará la moneda y el idioma que usarán las compras desde la aplicación al iniciar sesión como ese usuario.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "The Select iTunes Store field will determine the user's currency and language for their in-app purchases")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Recuperando información del producto

En el primer paso de la venta de un producto de compra desde la aplicación se muestra: recuperar el precio actual y la descripción de la tienda de aplicaciones para su visualización.   

Independientemente del tipo de productos que una aplicación vende (consumible, no consumible o de un tipo de suscripción), el proceso de recuperación de la información de producto para su presentación es el mismo. El código de InAppPurchaseSample que acompaña a este artículo contiene un proyecto denominado *consumibles* que muestra cómo recuperar la información de producción para su presentación. Muestra cómo:

- Cree una implementación de `SKProductsRequestDelegate` e implemente el método Abstract `ReceivedResponse`. El código de ejemplo llama a esta clase `InAppPurchaseManager`. 
- Compruebe con StoreKit para ver si se permiten pagos (con `SKPaymentQueue.CanMakePayments`). 
- Cree una instancia de un `SKProductsRequest` con los identificadores de producto que se han definido en iTunes Connect. Esto se hace en el método `InAppPurchaseManager.RequestProductData` del ejemplo. 
- Llame al método Start en el `SKProductsRequest`. Esto desencadena una llamada asincrónica a los servidores de App Store. Se llamará al delegado (`InAppPurchaseManager`) con los resultados. 
- El método de `ReceivedResponse` del delegado (`InAppPurchaseManager`) actualiza la interfaz de usuario con los datos devueltos de la tienda de aplicaciones (precios de productos & descripciones o mensajes sobre productos no válidos). 

La interacción general tiene el siguiente aspecto ( **StoreKit** está integrado en iOS y el **App Store** representa los servidores de Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Retrieving Product Information graph")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Ejemplo de visualización de la información del producto

El código de ejemplo de los *consumibles* de [InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) muestra cómo se puede recuperar la información del producto. En la pantalla principal del ejemplo se muestra información de dos productos que se recuperan de la tienda de aplicaciones:   

 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "The main screen displays information products  retrieved from the App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   

El código de ejemplo para recuperar y mostrar la información del producto se explica con más detalle a continuación.

#### <a name="viewcontroller-methods"></a>Métodos ViewController

La clase `ConsumableViewController` administrará la presentación de los precios de dos productos cuyos identificadores de producto están codificados en la clase.

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

En el nivel de clase, debe haber también un NSObject declarado que se usará para configurar un observador `NSNotificationCenter`:

```csharp
NSObject priceObserver;
```

En el método ViewWillAppear, el observador se crea y se asigna mediante el centro de notificaciones predeterminado:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

Al final del método `ViewWillAppear`, llame al método `RequestProductData` para iniciar la solicitud StoreKit. Una vez realizada esta solicitud, StoreKit se pondrá en contacto de forma asincrónica con los servidores de Apple para obtener la información y volver a introducirla en la aplicación. Esto se logra mediante la subclase `SKProductsRequestDelegate` (`InAppPurchaseManager`) que se explica en la sección siguiente.

```csharp
iap.RequestProductData(products);
```

El código para mostrar el precio y la descripción simplemente recupera la información de SKProduct y la asigna a los controles UIKit (Observe que se muestra el `LocalizedTitle` y `LocalizedDescription` – StoreKit resuelve automáticamente el texto y los precios correctos en función del configuración de la cuenta). El código siguiente pertenece a la notificación que hemos creado anteriormente:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

Por último, el método de `ViewWillDisappear` debe asegurarse de que se quita el observador:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Métodos SKProductRequestDelegate (InAppPurchaseManager)

Se llama al método `RequestProductData` cuando la aplicación desea recuperar precios de productos y otra información. Analiza la colección de identificadores de producto en el tipo de datos correcto y, a continuación, crea un `SKProductsRequest` con esa información. Al llamar al método Start, se realiza una solicitud de red a los servidores de Apple. La solicitud se ejecutará de forma asincrónica y llamará al método `ReceivedResponse` del delegado cuando se haya completado correctamente.

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS enrutará automáticamente la solicitud a la versión de "espacio aislado" o "producción" de la tienda de aplicaciones, en función del perfil de aprovisionamiento con el que se ejecute la aplicación, por lo que cuando desarrolle o pruebe la aplicación, la solicitud tendrá acceso a todos los productos. configurado en iTunes Connect (incluso los que todavía no se han enviado o aprobado por Apple). Cuando la aplicación se encuentra en producción, las solicitudes de StoreKit solo devolverán información de los productos **aprobados** .   

Se llama al método invalidado `ReceivedResponse` después de que los servidores de Apple hayan respondido con los datos. Dado que se llama en segundo plano, el código debe analizar los datos válidos y usar una notificación para enviar la información del producto a cualquier ViewControllers que esté "escuchando" en esa notificación. A continuación se muestra el código para recopilar información válida del producto y enviar una notificación:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

Aunque no se muestra en el diagrama, el método de `RequestFailed` también debe invalidarse para que pueda proporcionar algunos comentarios al usuario en caso de que los servidores de App Store no estén accesibles (o se produzca algún otro error). El código de ejemplo simplemente escribe en la consola, pero una aplicación real puede optar por consultar `error.Code` propiedad e implementar el comportamiento personalizado (como una alerta al usuario).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Esta captura de pantalla muestra la aplicación de ejemplo inmediatamente después de la carga (cuando no hay información de producto disponible):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "The sample app immediately after loading when no product information is available")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Productos no válidos

Una `SKProductsRequest` también puede devolver una lista de identificadores de producto no válidos. Normalmente, los productos no válidos se devuelven debido a una de las siguientes causas:   

El ID. del **producto tiene un tipo** incorrecto: solo se aceptan identificadores de producto válidos.   
   
 El **producto no** se ha aprobado: durante la prueba, todos los productos que se han desactivado para su venta deben ser devueltos por un `SKProductsRequest`; sin embargo, solo se devuelven los productos aprobados en producción.   
   
 El **ID. de aplicación no es explícito** : los identificadores de aplicación comodín (con un asterisco) no permiten la compra desde la aplicación.   
   
 **Perfil de aprovisionamiento incorrecto** : Si realiza cambios en la configuración de la aplicación en el portal de aprovisionamiento (por ejemplo, para habilitar compras desde la aplicación), recuerde volver a generar y usar el perfil de aprovisionamiento correcto al compilar la aplicación.   
   
 el **contrato de aplicaciones de pago de iOS no está en su lugar** : las características de StoreKit no funcionarán a menos que haya un contrato válido para su cuenta de desarrollador de Apple.   
   
 **El binario está en estado rechazado** : si hay un archivo binario enviado anteriormente en el estado rechazado (ya sea por el equipo de App Store o por el desarrollador), las características de StoreKit no funcionarán.

El método `ReceivedResponse` del código de ejemplo genera los productos no válidos en la consola:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Mostrar precios localizados

Los planes de tarifa especifican un precio específico para cada producto en todas las tiendas internacionales de aplicaciones. Para asegurarse de que los precios se muestran correctamente para cada moneda, use el siguiente método de extensión (definido en `SKProductExtension.cs`) en lugar de la propiedad Price de cada `SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

El código que establece el título del botón usa el método de extensión de la siguiente manera:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

El uso de dos cuentas de prueba de iTunes diferentes (una para la tienda norteamericana y otra para el almacén japonés) da como resultado las siguientes capturas de pantallas:   

 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Two different iTunes test accounts showing language specific results")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   

Tenga en cuenta que el almacén afecta al idioma que se usa para la información del producto y la moneda del precio, mientras que la configuración de idioma del dispositivo afecta a las etiquetas y a otro contenido localizado.   

Recuerde que para usar una cuenta de prueba de tienda diferente, debe **cerrar la sesión** en la **configuración > iTunes y App Store** y volver a iniciar la aplicación para iniciar sesión con una cuenta diferente. Para cambiar el idioma del dispositivo, vaya a **configuración > General > International > Language**.
