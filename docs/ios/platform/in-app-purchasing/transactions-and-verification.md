---
title: Transacciones y comprobación en Xamarin. iOS
description: En este documento se describe cómo permitir la restauración de las compras anteriores en una aplicación de Xamarin. iOS. También se describen las formas de proteger las compras y los productos entregados por el servidor.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 2a0d0e1ab7272094d55dff7fa083e61ee9c3286c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527591"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Transacciones y comprobación en Xamarin. iOS

## <a name="restoring-past-transactions"></a>Restaurar transacciones anteriores

Si la aplicación admite tipos de productos que son restaurables, debe incluir algunos elementos de la interfaz de usuario para permitir que los usuarios restauren esas compras.
Esta funcionalidad permite a un cliente agregar el producto a dispositivos adicionales o restaurar el producto en el mismo dispositivo después de borrarlo limpiando o quitando y volviendo a instalar la aplicación. Los siguientes tipos de productos son restaurables:

- Productos no consumibles
- Suscripciones autorenovables
- Suscripciones gratuitas

El proceso de restauración debe actualizar los registros que se mantienen en el dispositivo para completar los productos. El cliente puede elegir restaurar en cualquier momento, en cualquiera de sus dispositivos. El proceso de restauración vuelve a enviar todas las transacciones anteriores para ese usuario; a continuación, el código de aplicación debe determinar qué acción debe llevar a cabo con esa información (por ejemplo, comprobar si ya existe un registro de la compra en el dispositivo y, si no es así, crear un registro de la compra y habilitar el producto para el usuario).

### <a name="implementing-restore"></a>Implementar la restauración

El botón **restaurar** de la interfaz de usuario llama al método siguiente, que desencadena RestoreCompletedTransactions `SKPaymentQueue`en.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit enviará la solicitud de restauración a los servidores de Apple de forma asincrónica.   
   
Dado que `CustomPaymentObserver` se registra como un observador de transacción, recibirá mensajes cuando los servidores de Apple respondan. La respuesta contendrá todas las transacciones que el usuario ha realizado alguna vez en esta aplicación (en todos sus dispositivos). El código recorre en bucle cada transacción, detecta el estado restaurado y llama `UpdatedTransactions` al método para procesarlo, tal y como se muestra a continuación:

```csharp
// called when the transaction status is updated
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
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

Si no hay ningún producto restaurable para el usuario, `UpdatedTransactions` no se llama a.   
   
El código más sencillo posible para restaurar una transacción determinada en el ejemplo realiza las mismas acciones que cuando se realiza una compra, con la excepción `OriginalTransaction` de que se usa la propiedad para tener acceso al ID. de producto:

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

Una implementación más sofisticada puede comprobar `transaction.OriginalTransaction` otras propiedades, como la fecha original y el número de recepción. Esta información será útil para algunos tipos de productos (como suscripciones).

#### <a name="restore-completion"></a>Finalización de restauración

`CustomPaymentObserver` Tiene dos métodos adicionales a los que StoreKit llamará cuando se complete el proceso de restauración (ya sea correctamente o con errores), como se muestra a continuación:

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

En el ejemplo, estos métodos no hacen nada, pero una aplicación real puede optar por implementar un mensaje para el usuario o alguna otra funcionalidad.

## <a name="securing-purchases"></a>Protección de compras

Los dos ejemplos de este documento usan `NSUserDefaults` para realizar el seguimiento de las compras:   
   
 **Consumibles** : el "saldo" de las compras de `NSUserDefaults` crédito es un valor entero simple que se incrementa con cada compra.   
   
 **No consumibles** : cada compra de filtro fotográfico se almacena como un par clave-valor `NSUserDefaults`en.

El `NSUserDefaults` uso de mantiene el código de ejemplo simple, pero no ofrece una solución muy segura, ya que es posible que los usuarios con conocimientos técnicos actualicen la configuración (omitiendo el mecanismo de pago).   
   
Nota: Las aplicaciones del mundo real deben adoptar un mecanismo seguro para almacenar contenido adquirido que no está sujeto a alteraciones del usuario. Esto puede implicar el cifrado y/u otras técnicas, incluida la autenticación de servidor remoto.   
   
 El mecanismo también debe diseñarse para aprovechar las ventajas de las características integradas de copia de seguridad y recuperación de iOS, iTunes e iCloud. Esto garantizará que, una vez que un usuario restaura una copia de seguridad, sus compras anteriores estarán disponibles inmediatamente.   
   
Consulte la guía de codificación segura de Apple para obtener más instrucciones específicas de iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Comprobación de la recepción y productos entregados por el servidor

Los ejemplos de este documento hasta el momento han formado únicamente la aplicación que se comunica directamente con los servidores de App Store para realizar transacciones de compra, que desbloquean características o funcionalidades ya codificadas en la aplicación.   
   
Apple ofrece un nivel adicional de seguridad de compra, ya que permite que otro servidor Compruebe de forma independiente las recepciones de compra, lo que puede resultar útil para validar una solicitud antes de entregar contenido digital como parte de una compra (por ejemplo, un libro digital o revista).   
   
 **Productos integrados** : como los ejemplos de este documento, el producto que se está adquiriendo existe como funcionalidad incluida en la aplicación. Una compra desde la aplicación permite al usuario tener acceso a la funcionalidad.
Los identificadores de producto están codificados.   
   
 **Productos entregados** por el servidor: el producto consta de contenido descargable que se almacena en un servidor remoto hasta que una transacción correcta hace que se descargue el contenido.
Algunos ejemplos pueden incluir problemas de libros o revistas. Los identificadores de producto normalmente se incluyen en un servidor externo (donde también se hospeda el contenido del producto). Las aplicaciones deben implementar una manera robusta de grabar cuando se ha completado una transacción, de modo que si se produce un error en la descarga de contenido, se puede volver a intentar sin confundir al usuario.

### <a name="server-delivered-products"></a>Productos proporcionados por el servidor

El contenido de algún producto, como libros y revistas (o incluso un nivel de juego), debe descargarse desde un servidor remoto durante el proceso de compra. Esto significa que se requiere un servidor adicional para almacenar y proporcionar el contenido del producto una vez adquirido.

#### <a name="getting-prices-for-server-delivered-products"></a>Obtención de precios para productos proporcionados por el servidor

Dado que los productos se entregan de forma remota, también es posible agregar más productos a lo largo del tiempo (sin actualizar el código de la aplicación), como agregar más libros o nuevos problemas de una revista. Para que la aplicación pueda detectar estos productos de noticias y mostrarlos al usuario, el servidor adicional debe almacenar y proporcionar esta información.   
   
[![](transactions-and-verification-images/image38.png "Obtención de precios para productos proporcionados por el servidor")](transactions-and-verification-images/image38.png#lightbox)   
   
1. La información del producto debe almacenarse en varios lugares: en el servidor y en iTunes Connect. Además, cada producto tendrá archivos de contenido asociados. Estos archivos se entregarán después de una compra correcta.   
   
2. Cuando el usuario desea comprar un producto, la aplicación debe determinar qué productos están disponibles. Esta información se puede almacenar en caché, pero se debe entregar desde un servidor remoto donde se almacena la lista maestra de productos.   
   
3. El servidor devuelve una lista de identificadores de producto para la aplicación que se va a analizar.   
   
4. A continuación, la aplicación determina cuáles de estos ID. de producto se enviarán a StoreKit para recuperar precios y descripciones.   
   
5. StoreKit envía la lista de identificadores de producto a los servidores de Apple.   
   
6. Los servidores de iTunes responden con información de producto válida (Descripción y precio actual).   
   
7. A la aplicación `SKProductsRequestDelegate` se le pasa la información del producto para mostrarla al usuario.

#### <a name="purchasing-server-delivered-products"></a>Compra de productos proporcionados por el servidor

Dado que el servidor remoto requiere alguna manera de validar que una solicitud de contenido es válida (es decir, se ha pagado por), la información de recepción se pasa para la autenticación. El servidor remoto reenvía esos datos a iTunes para su comprobación y, si se realiza correctamente, incluye el contenido del producto en la respuesta a la aplicación.   
   
 [![](transactions-and-verification-images/image39.png "Compra de productos proporcionados por el servidor")](transactions-and-verification-images/image39.png#lightbox)   
   
1. La aplicación agrega un `SKPayment` a la cola. Si es necesario, se solicitará al usuario su identificador de Apple y se le pedirá que confirme el pago.   
   
2. StoreKit envía la solicitud al servidor para su procesamiento.   
   
3. Una vez completada la transacción, el servidor responde con una confirmación de transacción.   
   
4. La `SKPaymentTransactionObserver` subclase recibe la confirmación y la procesa. Dado que el producto se debe descargar desde un servidor, la aplicación inicia una solicitud de red al servidor remoto.   
   
5. La solicitud de descarga va acompañada de los datos de recepción para que el servidor remoto pueda comprobar que está autorizado para tener acceso al contenido. El cliente de red de la aplicación espera una respuesta a esta solicitud.   
   
6. Cuando el servidor recibe una solicitud de contenido, analiza los datos de recepción y envía una solicitud directamente a los servidores iTunes para comprobar que la recepción es para una transacción válida. El servidor debe usar alguna lógica para determinar si se va a enviar la solicitud a la dirección URL de producción o de espacio aislado. Apple sugiere usar siempre la dirección URL de producción y cambiar al espacio aislado si su estado de recepción 21007 (recepción de espacio aislado enviada al servidor de producción). Consulte la guía de [programación](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) de la validación de recepciones de Apple para obtener más detalles.
   
7. iTunes comprobará la recepción y devolverá un estado de cero si es válido.   
   
8. El servidor espera la respuesta de iTunes. Si recibe una respuesta válida, el código debe localizar el archivo de contenido del producto asociado que se va a incluir en la respuesta a la aplicación.   
  
9. La aplicación recibe y analiza la respuesta, guardando el contenido del producto en el sistema de archivos del dispositivo.   
   
10. La aplicación habilita el producto y, a continuación, llama `FinishTransaction`a StoreKit. A continuación, la aplicación puede mostrar opcionalmente el contenido comprado (por ejemplo, Mostrar la primera página de un problema del libro o de una revista comprada).

Una implementación alternativa para los archivos de contenido de productos muy grandes podría implicar simplemente almacenar la recepción de la transacción en el paso #9 para que la transacción se pueda completar rápidamente y proporcionar una interfaz de usuario para que el usuario descargue el contenido real del producto. en algún momento posterior. La solicitud de descarga posterior puede volver a enviar la confirmación almacenada para tener acceso al archivo de contenido del producto requerido.

### <a name="writing-server-side-receipt-verification-code"></a>Escribir código de verificación de recepción del servidor

La validación de una recepción en el código del servidor se puede realizar con una solicitud/respuesta HTTP POST simple que abarque los pasos #5 a través de #8 en el diagrama de flujo de trabajo.   
   
Extraiga la `SKPaymentTansaction.TransactionReceipt` propiedad en la aplicación. Estos son los datos que se deben enviar a iTunes para su comprobación (paso #5).

Codifique en Base64 los datos de recepción de la transacción (ya sea en el paso #5 o #6).

Cree una carga JSON simple como la siguiente:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP envíe el JSON a [https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt) para producción o [https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt) para pruebas.   
   
 La respuesta JSON contendrá las claves siguientes:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Un estado cero indica una confirmación válida. El servidor puede continuar para completar el contenido del producto adquirido. La clave Receipt contiene un diccionario JSON con las mismas propiedades que el `SKPaymentTransaction` objeto que recibió la aplicación, por lo que el código del servidor puede consultar este diccionario para recuperar información como el product_id y la cantidad de la compra.

Consulte la documentación de la [Guía de programación](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) de la validación de recepciones de Apple para obtener información adicional.
