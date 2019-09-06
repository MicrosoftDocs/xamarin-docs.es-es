---
title: Solicitar revisión de la aplicación en Xamarin. iOS
description: En este artículo se describe el método RequestReview que Apple agregó a iOS 10 y se describe cómo implementarlo en Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 146dc66974f6e0c6e6a8f7bf9f46b570025eaead
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280387"
---
# <a name="request-app-review-in-xamarinios"></a>Solicitar revisión de la aplicación en Xamarin. iOS

_En este artículo se describe el método RequestReview que Apple ha agregado a iOS 10 y cómo implementarlo en Xamarin. iOS._

Novedad de iOS 10,3, el `RequestReview()` método permite que una aplicación de iOS solicite al usuario que califique o revise. Cuando se llama a este método en una aplicación de envío que el usuario ha instalado desde la tienda de aplicaciones, iOS 10 tratará todo el proceso de evaluación y valoración del desarrollador. Dado que este proceso se rige por la Directiva de App Store, es posible que se muestre una alerta.

![](request-app-review-images/review01.png "Una alerta de solicitud de revisión de ejemplo")

## <a name="requesting-a-rating-or-review"></a>Solicitud de una clasificación o revisión

Aunque se puede llamar al método `SKStoreReviewController` estáticodelaclaseencualquierpuntoenelquetengasentidoenlaexperienciadelusuario,elprocesoderevisiónserigeycontrolamediantelaDirectivadeAppStore.`RequestReview()` Como resultado, este método puede o no mostrar una alerta y nunca debe llamarse como respuesta a una acción del usuario, como pulsar un botón.

Por ejemplo, una aplicación puede solicitar una revisión una vez que se ha iniciado un número determinado de veces, o un juego puede solicitar una revisión una vez que el jugador finaliza un nivel.

Para solicitar una revisión en cuanto finalice el inicio de una aplicación de Xamarin. iOS, realice los cambios siguientes `AppDelegate.cs` en el archivo:

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }

        ...

    }
}
```

> [!NOTE]
> Llamar `RequestReview()` a en una aplicación en desarrollo siempre mostrará el cuadro de diálogo clasificación y revisión para que se pueda probar. Esto no se aplica a las aplicaciones que se han distribuido a través de TestFlight, donde se omitirá la llamada al método.

Cuando se llama al métodoenunaaplicacióndeenvíoqueelusuariohainstaladodesdelatiendadeaplicaciones,iOS10tratarátodoelprocesodeevaluaciónyvaloracióndeldesarrollador.`RequestReview()` De nuevo, dado que este proceso se rige por la Directiva de App Store, es posible que se muestre una alerta.

## <a name="linking-to-an-app-store-product-page"></a>Vínculo a una página del producto de la tienda de aplicaciones 

Además del nuevo `RequestReview` método, el desarrollador todavía puede proporcionar un vínculo profundo a la página del producto de la aplicación en la tienda de aplicaciones desde una aplicación. Al anexar `action=write-review` al final de la dirección URL de la página del producto, se abrirá una página en la que el usuario puede escribir una revisión de la aplicación automáticamente. 

## <a name="summary"></a>Resumen

En este artículo se ha tratado el método RequestReview que Apple ha agregado a iOS 10 y cómo implementarlo en Xamarin. iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
