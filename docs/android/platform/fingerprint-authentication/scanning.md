---
title: Buscando huellas digitales
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027491"
---
# <a name="scanning-for-fingerprints"></a>Buscando huellas digitales

Ahora que hemos aprendido a preparar una aplicación de Xamarin. Android para usar la autenticación mediante huella digital, vamos a volver al método `FingerprintManager.Authenticate` y analizaremos su lugar en la autenticación de huellas digitales de Android 6,0. En esta lista se describe información general rápida sobre el flujo de trabajo para la autenticación mediante huellas digitales:

1. Invocar `FingerprintManager.Authenticate`, pasando un `CryptoObject` y una instancia de `FingerprintManager.AuthenticationCallback`. El `CryptoObject` se utiliza para asegurarse de que el resultado de la autenticación de huellas digitales no se ha alterado. 
2. Subclase de la clase [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) . Se proporcionará una instancia de esta clase para `FingerprintManager` cuando se inicie la autenticación mediante huella digital. Una vez finalizado el escáner de huellas digitales, se invocará uno de los métodos de devolución de llamada en esta clase.
3. Escriba código para actualizar la interfaz de usuario para que el usuario sepa que el dispositivo ha iniciado el escáner de huellas digitales y está esperando la interacción con el usuario. 
4. Cuando se realiza el escáner de huellas digitales, Android devolverá los resultados a la aplicación invocando un método en la instancia de `FingerprintManager.AuthenticationCallback` que se proporcionó en el paso anterior.
5. La aplicación informará al usuario de los resultados de la autenticación mediante huellas digitales y reaccionará a los resultados según corresponda. 

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

Analicemos cada uno de estos parámetros en el método `Authenticate` en un poco más detallado:

- El primer parámetro es un objeto _criptográfico_ que el escáner de huellas digitales usará para ayudar a autenticar los resultados de un análisis de huellas digitales. Este objeto se puede `null`, en cuyo caso la aplicación tiene que confiar ciegamente en que nada ha alterado los resultados de la huella digital. Se recomienda crear una instancia de `CryptoObject` y proporcionarla al `FingerprintManager` en lugar de NULL. En la [creación de un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) se explicará en detalle cómo crear una instancia de un `CryptoObject` basado en un `Cipher`.
- El segundo parámetro siempre es cero. La documentación de Android lo identifica como un conjunto de marcas y lo más probable es que esté reservado para un uso futuro. 
- El tercer parámetro, `cancellationSignal` es un objeto que se usa para desactivar el escáner de huellas digitales y cancelar la solicitud actual. Se trata de un [CancellationSignal de Android](https://developer.android.com/reference/android/os/CancellationSignal.html), y no de un tipo de .NET Framework.
- El cuarto parámetro es obligatorio y es una clase que subclase la clase abstracta `AuthenticationCallback`. Los métodos de esta clase se invocarán para indicar a los clientes Cuándo ha finalizado la `FingerprintManager` y cuáles son los resultados. Como hay mucho que entender sobre cómo implementar el `AuthenticationCallback`, se tratará en [su propia sección](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- El quinto parámetro es una instancia de `Handler` opcional. Si se proporciona un objeto de `Handler`, el `FingerprintManager` usará el `Looper` de ese objeto al procesar los mensajes del hardware de huella digital. Normalmente, no es necesario proporcionar un `Handler`, FingerprintManager usará el `Looper` de la aplicación.

## <a name="cancelling-a-fingerprint-scan"></a>Cancelar un examen de huellas digitales

Es posible que sea necesario que el usuario (o la aplicación) cancele el análisis de huellas digitales una vez iniciado. En esta situación, invoque el método [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) en el [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) que se proporcionó para `FingerprintManager.Authenticate` cuando se invocó para iniciar el análisis de huellas digitales.

Ahora que hemos encontrado el método `Authenticate`, vamos a examinar algunos de los parámetros más importantes con más detalle. En primer lugar, veremos la [respuesta a las devoluciones de llamada de autenticación](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), que explican cómo crear subclases de [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), lo que permite que una aplicación de Android reaccione a los resultados proporcionados por el escáner de huellas digitales.

## <a name="related-links"></a>Vínculos relacionados

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
