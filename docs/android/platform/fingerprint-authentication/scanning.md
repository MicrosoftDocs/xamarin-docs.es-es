---
title: Examen de huellas digitales
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027491"
---
# <a name="scanning-for-fingerprints"></a>Examen de huellas digitales

Ahora que hemos aprendido a preparar una aplicación de Xamarin.Android para usar la autenticación con huella digital, vamos a volver al método `FingerprintManager.Authenticate` y a analizar su lugar en la autenticación con huella digital de Android 6.0. En esta lista se describe información general rápida sobre el flujo de trabajo para la autenticación con huella digital:

1. Invoque a `FingerprintManager.Authenticate`, pasando `CryptoObject` y una instancia de `FingerprintManager.AuthenticationCallback`. `CryptoObject` se utiliza para asegurarse de que el resultado de la autenticación de huella digital no se ha alterado. 
2. Cree una subclase de la clase [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html). Se proporcionará una instancia de esta clase a `FingerprintManager` cuando se inicie la autenticación con huella digital. Una vez que el examen de la huella digital haya finalizado, se invocará a uno de los métodos de devolución de llamada en esta clase.
3. Escriba código para actualizar la interfaz de usuario para que el usuario sepa que el dispositivo ha iniciado el examen de la huella digital y está esperando la interacción del usuario. 
4. Una vez realizado el examen de la huella digital, Android devolverá los resultados a la aplicación invocando a un método en la instancia de `FingerprintManager.AuthenticationCallback` que se proporcionó en el paso anterior.
5. La aplicación informará al usuario de los resultados de la autenticación con huella digital y reaccionará a los resultados según corresponda. 

El siguiente fragmento de código es un ejemplo de un método en una actividad que iniciará el examen de huellas digitales:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Analicemos cada uno de estos parámetros del método `Authenticate` con más detalle:

- El primer parámetro es un objeto _crypto_ que utilizará el examen escáner de huellas digitales para ayudar a autenticar los resultados de un examen de huellas digitales. Este objeto puede ser `null`, en cuyo caso la aplicación tiene que confiar ciegamente en que nada haya alterado los resultados de la huella digital. Se recomienda crear una instancia de `CryptoObject` y proporcionarla a `FingerprintManager` en lugar de null. En [Creación de un CryptoObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) se explicará en detalle cómo crear una instancia de `CryptoObject` basándose en `Cipher`.
- El segundo parámetro es siempre cero. La documentación de Android identifica esto como un conjunto de marcas y lo más probable es que se reserve para un uso futuro. 
- El tercer parámetro, `cancellationSignal`, es un objeto que se usa para desactivar el escáner de huellas digitales y cancelar la solicitud actual. Se trata de [CancellationSignal de Android](https://developer.android.com/reference/android/os/CancellationSignal.html), y no de un tipo de .NET Framework.
- El cuarto parámetro es obligatorio y es una clase que crea una subclase de la clase abstracta `AuthenticationCallback`. Los métodos de esta clase se invocarán para indicar a los clientes cuándo `FingerprintManager` ha finalizado y cuáles son los resultados. Como hay mucho que entender sobre cómo implementar `AuthenticationCallback`, se tratará en [su propia sección](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- El quinto parámetro es una instancia de `Handler` opcional. Si se proporciona un objeto `Handler`, `FingerprintManager` usará `Looper` de ese objeto al procesar los mensajes del hardware de huella digital. Normalmente, no es necesario proporcionar `Handler`, FingerprintManager usará `Looper` de la aplicación.

## <a name="cancelling-a-fingerprint-scan"></a>Cancelar un examen de huella digital

Es posible que sea necesario que el usuario (o la aplicación) cancele el examen de huellas digitales una vez iniciado. En esta situación, invoque al método [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) en [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) que se proporcionó a `FingerprintManager.Authenticate` cuando se invocó para iniciar el examen de huellas digitales.

Ahora que hemos encontrado el método `Authenticate`, vamos a examinar algunos de los parámetros más importantes con más detalle. En primer lugar, veremos [Respuesta a las devoluciones de llamada de autenticación](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), donde se explica cómo crear subclases [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), lo que permite a una aplicación de Android reaccionar a los resultados que proporcionada el escáner de huellas digitales.

## <a name="related-links"></a>Vínculos relacionados

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
