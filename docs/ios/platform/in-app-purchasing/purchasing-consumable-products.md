---
title: Compra de productos consumibles en Xamarin. iOS
description: En este documento se describen los productos consumibles en Xamarin. iOS. Los productos consumibles son partes de funcionalidad de un solo uso, como la moneda del juego.
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 5af4ba8057070481728948635352e1ec2484a0d4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032336"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>Compra de productos consumibles en Xamarin. iOS

Los productos consumibles son la más sencilla de implementar, ya que no hay ningún requisito de "restauración". Son útiles para productos como la moneda del juego o una parte de la funcionalidad de un solo uso. Los usuarios pueden volver a comprar productos consumibles una y otra vez.

## <a name="built-in-product-delivery"></a>Entrega de productos integrada

En el código de ejemplo que acompaña a este documento se muestran los productos integrados: los identificadores de producto están codificados en la aplicación porque están estrechamente acoplados al código que "desbloquea" la característica después del pago. El proceso de compra se puede visualizar de la siguiente manera:   
   
[![la visualización del proceso de compra](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 El flujo de trabajo básico es:   
   
 1. La aplicación agrega un `SKPayment` a la cola. Si es necesario, se solicitará al usuario su identificador de Apple y se le pedirá que confirme el pago.   
   
 2. StoreKit envía la solicitud al servidor para su procesamiento.   
   
 3. Una vez completada la transacción, el servidor responde con una confirmación de transacción.   
   
 4. La subclase `SKPaymentTransactionObserver` recibe la confirmación y la procesa.   
   
 5. La aplicación habilita el producto (actualizando `NSUserDefaults` o algún otro mecanismo) y, a continuación, llama a la `FinishTransaction`de StoreKit.

Hay otro tipo de flujo de trabajo: *productos entregados por el servidor* , que se describe más adelante en el documento (consulte la sección comprobación de la *recepción y productos entregados por el servidor*).

## <a name="consumable-products-example"></a>Ejemplo de productos consumibles

El [código InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contiene un proyecto denominado *consumibles* que implementa una "moneda del juego" básica (denominada "créditos de Monkey"). En el ejemplo se muestra cómo implementar dos productos de compras desde la aplicación para que el usuario pueda comprar tantos "créditos de Monkey" como deseen: en una aplicación real, también sería cierto modo de gastarlos.   

La aplicación se muestra en estas capturas de pantallas: cada compra agrega más "créditos de Monkey" al saldo del usuario:   

 [![cada compra agrega más créditos de Monkey al saldo de los usuarios](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   

Las interacciones entre las clases personalizadas, StoreKit y App Store tienen el siguiente aspecto:   

 [![las interacciones entre las clases personalizadas, StoreKit y App Store](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

### <a name="viewcontroller-methods"></a>Métodos ViewController

Además de las propiedades y los métodos necesarios para recuperar la información del producto, el controlador de vista requiere observadores de notificación adicionales para escuchar las notificaciones relacionadas con las compras. Solo se `NSObjects` que se registrarán y quitarán en `ViewWillAppear` y `ViewWillDisappear` respectivamente.

```csharp
NSObject succeededObserver, failedObserver;
```

El constructor también creará la subclase `SKProductsRequestDelegate` (`InAppPurchaseManager`) que, a su vez, crea y registra el `SKPaymentTransactionObserver` (`CustomPaymentObserver`).   

La primera parte del procesamiento de una transacción de compra desde la aplicación es controlar la pulsación del botón cuando el usuario desea comprar algo, tal y como se muestra en el código siguiente de la aplicación de ejemplo:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

La segunda parte de la interfaz de usuario controla la notificación de que la transacción se ha realizado correctamente, en este caso actualizando el saldo mostrado:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

La parte final de la interfaz de usuario muestra un mensaje si se cancela una transacción por algún motivo. En el código de ejemplo, un mensaje simplemente se escribe en la ventana de salida:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Además de estos métodos en el controlador de vista, una transacción de compra de productos consumible también requiere código en el `SKProductsRequestDelegate` y en el `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Métodos InAppPurchaseManager

El código de ejemplo implementa un número de métodos relacionados con la compra en la clase InAppPurchaseManager, incluido el método `PurchaseProduct` que crea una instancia de `SKPayment` y la agrega a la cola para su procesamiento:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Agregar el pago a la cola es una operación asincrónica. La aplicación vuelve a obtener el control mientras StoreKit procesa la transacción y la envía a los servidores de Apple. En este momento, iOS comprobará que el usuario ha iniciado sesión en la tienda de aplicaciones y le pedirá un identificador de Apple y una contraseña, si es necesario.   

Suponiendo que el usuario se autentique correctamente en la tienda de aplicaciones y acepte la transacción, el `SKPaymentTransactionObserver` recibirá la respuesta de StoreKit y llamará al método siguiente para completar la transacción y finalizarla.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

El último paso es asegurarse de que notifica a StoreKit que ha cumplido correctamente la transacción, llamando a `FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

Una vez entregado el producto, se debe llamar a `SKPaymentQueue.DefaultQueue.FinishTransaction` para quitar la transacción de la cola de pago.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Métodos SKPaymentTransactionObserver (CustomPaymentObserver)

StoreKit llama al método `UpdatedTransactions` cuando recibe una respuesta de los servidores de Apple y pasa una matriz de objetos `SKPaymentTransaction` que el código debe inspeccionar. El método recorre en bucle cada transacción y realiza una función diferente en función del estado de la transacción (como se muestra aquí):

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
              theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
              theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

El método `CompleteTransaction` se explicó anteriormente en esta sección: guarda los detalles de la compra en `NSUserDefaults`, finaliza la transacción con StoreKit y, por último, notifica a la interfaz de usuario que se actualice.

### <a name="purchasing-multiple-products"></a>Compra de varios productos

Si tiene sentido en la aplicación comprar varios productos, utilice la clase `SKMutablePayment` y establezca el campo Quantity:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

El código que controla la transacción completada también debe consultar la propiedad quantity para completar correctamente la compra:

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

Cuando el usuario adquiere varias cantidades, la alerta de confirmación de StoreKit reflejará la cantidad, el precio unitario y el precio total que se le cobrará, como se muestra en la siguiente captura de pantalla:

[![confirmar una compra](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Control de interrupciones de la red

Las compras desde la aplicación requieren una conexión de red operativa para que StoreKit se comunique con los servidores de Apple. Si no hay disponible una conexión de red, la compra desde la aplicación no estará disponible.

### <a name="product-requests"></a>Solicitudes de producto

Si la red no está disponible mientras se realiza una `SKProductRequest`, se llamará al método `RequestFailed` de la subclase `SKProductsRequestDelegate` (`InAppPurchaseManager`), como se muestra a continuación:

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

A continuación, el ViewController escucha la notificación y muestra un mensaje en los botones de compra:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Dado que una conexión de red puede ser transitoria en dispositivos móviles, es posible que las aplicaciones deseen supervisar el estado de la red mediante el marco de trabajo de SystemConfiguration y volver a intentar cuando haya disponible una conexión de red. Consulte a Apple o al que lo usa.

### <a name="purchase-transactions"></a>Transacciones de compra

La cola de pago de StoreKit almacenará y reenviará las solicitudes de compra si es posible, por lo que el efecto de una interrupción de la red variará en función de que se produzca un error en la red durante el proceso de compra.   

Si se produce un error durante una transacción, la subclase `SKPaymentTransactionObserver` (`CustomPaymentObserver`) tendrá el método `UpdatedTransactions` llamado y la clase `SKPaymentTransaction` estará en estado de error.

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
               theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
               theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

El método `FailedTransaction` detecta si el error se debe a la cancelación del usuario, como se muestra aquí:

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

Incluso si se produce un error en una transacción, se debe llamar al método `FinishTransaction` para quitar la transacción de la cola de pago:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

Después, el código de ejemplo envía una notificación para que ViewController pueda mostrar un mensaje. Las aplicaciones no deben mostrar un mensaje adicional si el usuario canceló la transacción. Otros códigos de error que pueden producirse son:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Control de restricciones

La **configuración > característica de restricciones de > general** de iOS permite a los usuarios bloquear determinadas características de su dispositivo.   

Puede consultar si el usuario tiene permiso para realizar compras desde la aplicación mediante el método `SKPaymentQueue.CanMakePayments`. Si esto devuelve false, el usuario no puede acceder a la compra desde la aplicación. StoreKit mostrará automáticamente un mensaje de error al usuario si se intenta realizar una compra. Al seleccionar este valor, la aplicación puede ocultar los botones de compra o realizar alguna otra acción para ayudar al usuario.   

En el archivo `InAppPurchaseManager.cs` el método `CanMakePayments` encapsula la función StoreKit de la siguiente manera:

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Para probar este método, use la característica de **restricciones** de iOS para deshabilitar las **compras desde la aplicación**:   

 [![usar la característica de restricciones de iOS para deshabilitar las compras desde la aplicación](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   

Este código de ejemplo de `ConsumableViewController` reacciona a `CanMakePayments` devolver false mostrando el texto **deshabilitado de AppStore** en los botones deshabilitados.

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

La aplicación tiene este aspecto cuando la característica **de compras desde la aplicación** está restringida: los botones de compra están deshabilitados.   

 [![la aplicación tiene este aspecto cuando la característica de compras desde la aplicación está restringida, los botones de compra están deshabilitados.](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   

La información del producto puede seguir siendo solicitada cuando `CanMakePayments` es false, por lo que la aplicación todavía puede recuperar y mostrar los precios. Esto significa que, si quitamos la `CanMakePayments` comprobación del código, los botones de compra seguirán estando activos, sin embargo, cuando se intenta realizar una compra, el usuario verá un mensaje que indica que **no se permiten compras desde la aplicación** (generado por StoreKit cuando la cola de pago es acceso:   

 [no se permiten![compras desde la aplicación](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   

Las aplicaciones del mundo real pueden adoptar un enfoque diferente para controlar la restricción, como ocultar los botones por completo y, quizás, ofrecer un mensaje más detallado que la alerta que StoreKit muestra automáticamente.
