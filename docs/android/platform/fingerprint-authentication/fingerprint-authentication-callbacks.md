---
title: Respuesta a las devoluciones de llamada de autenticación
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027530"
---
# <a name="responding-to-authentication-callbacks"></a>Respuesta a las devoluciones de llamada de autenticación

El escáner de huellas digitales se ejecuta en segundo plano en su propio subproceso y, cuando finaliza, informa de los resultados del análisis mediante la invocación de un método de `FingerprintManager.AuthenticationCallback` en el subproceso de interfaz de usuario. Una aplicación de Android debe proporcionar su propio controlador, que amplía esta clase abstracta e implementa todos los métodos siguientes:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Se invoca cuando se produce un error irrecuperable. No hay nada más que una aplicación o usuario pueda hacer para corregir la situación, excepto, quizás, volver a intentarlo.
- **`OnAuthenticationFailed()`** &ndash; Este método se invoca cuando se detecta una huella digital, pero el dispositivo no la reconoce.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Se invoca cuando se produce un error recuperable; por ejemplo, el dedo se desliza demasiado rápido por el escáner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Se invoca cuando se reconoce una huella digital.

Si se usó `CryptoObject` al llamar a `Authenticate`, se recomienda llamar a `Cipher.DoFinal` en `OnAuthenticationSuccessful`.
`DoFinal` generará una excepción si el cifrado se alteró o se inicializó incorrectamente, lo que indica que el resultado del escáner de huellas digitales puede haberse alterado fuera de la aplicación.

> [!NOTE]
> Se recomienda mantener la clase de devolución de llamada relativamente ligera y libre de lógica específica de la aplicación. Las devoluciones de llamada deben actuar como "policía de tráfico" entre la aplicación para Android y los resultados del escáner de huellas digitales.

## <a name="a-sample-authentication-callback-handler"></a>Controlador de devolución de llamada de autenticación de muestra

A continuación se muestra un ejemplo de implementación mínima de `FingerprintManager.AuthenticationCallback`: 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded` comprueba si se proporcionó un valor de `Cipher` para `FingerprintManager` cuando se invocó `Authentication`. En ese caso, se llama al método `DoFinal` en el cifrado. Esto cierra `Cipher` y lo restaura a su estado original. Si se produjo un problema con el cifrado, `DoFinal` devolverá una excepción y se debe considerar que el intento de autenticación ha generado un error.

Las devoluciones de llamada `OnAuthenticationError` y `OnAuthenticationHelp` reciben un entero que indica cuál era el problema. En la siguiente sección se explica cada uno de los posibles códigos de ayuda o error. Las dos devoluciones de llamada sirven para propósitos similares: para informar a la aplicación de que la autenticación mediante huella digital ha devuelto un error. La diferencia está en la gravedad. `OnAuthenticationHelp` es un error recuperable por el usuario, como el deslizamiento de la huella digital demasiado rápido, mientras que `OnAuthenticationError` es más un error grave, como un escáner de huellas digitales dañado.

Tenga en cuenta que se invocará `OnAuthenticationError` cuando se cancele la detección de huellas digitales a través del mensaje `CancellationSignal.Cancel()`. El parámetro `errMsgId` tendrá el valor 5 (`FingerprintState.ErrorCanceled`). En función de los requisitos, una implementación de `AuthenticationCallbacks` puede tratar esta situación de forma distinta a la del resto de errores. 

`OnAuthenticationFailed` se invoca cuando la huella digital se ha detectado correctamente, pero no coincide con ninguna huella digital inscrita en el dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Códigos de ayuda e id. de mensajes de error 

Puede encontrar una lista y una descripción de los códigos de error y los códigos de ayuda en la [documentación de Android SDK](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) de la clase FingerprintManager. Xamarin.Android representa estos valores con la enumeración `Android.Hardware.Fingerprints.FingerprintState`:

- **`AcquiredGood`** &ndash; (valor 0) La imagen adquirida era buena.

- **`AcquiredImagerDirty`** &ndash; (valor 3) La imagen de huella digital tenía demasiado ruido debido a la suciedad que se sospecha o se ha detectado que tiene el sensor. Por ejemplo, es razonable devolverlo después de varios valores `AcquiredInsufficient` o de la detección real de suciedad en el sensor (píxeles atascados, franjas, etc.). Se espera que el usuario tome medidas para limpiar el sensor cuando se devuelve este valor.

- **`AcquiredInsufficient`** &ndash; (valor 2) La imagen de huella digital tenía demasiado ruido para procesarla debido a una condición detectada (p. ej., piel seca) o un sensor posiblemente sucio (consulte `AcquiredImagerDirty`).

- **`AcquiredPartial`** &ndash; (valor 1) Solo se ha detectado una imagen de huella digital parcial. Durante la inscripción, se debe informar al usuario sobre lo que debe ocurrir para resolver este problema; por ejemplo, &ldquo;presione firmemente el sensor.&rdquo;

- **`AcquiredTooFast`** &ndash; (valor 5) La imagen de huella digital estaba incompleta debido a un movimiento rápido. Aunque suele corresponder a sensores de matriz lineal, también puede pasar si se mueve el dedo durante la detección. Se debe pedir al usuario que mueva el dedo más despacio (lineal) o que lo deje en el sensor más tiempo.

- **`AcquiredToSlow`** &ndash; (valor 4) La imagen de huella digital no se pudo leer debido a la falta de movimiento. Esto es lo más adecuado para los sensores de matriz lineal que requieren un movimiento de deslizamiento.

- **`ErrorCanceled`** &ndash; (valor 5) La operación se canceló porque el sensor de huellas digitales no está disponible. Por ejemplo, esto puede ocurrir cuando se cambia el usuario, se bloquea el dispositivo u otra operación pendiente lo impide o deshabilita.

- **`ErrorHwUnavailable`** &ndash; (valor 1) El hardware no está disponible. Vuelva a intentarlo más tarde.

- **`ErrorLockout`** &ndash; (valor 7) La operación se canceló porque la API está bloqueada debido a demasiados intentos.

- **`ErrorNoSpace`** &ndash; (valor 4) Estado de error devuelto para operaciones como la inscripción; no se puede completar la operación porque no queda suficiente espacio de almacenamiento para completarla.

- **`ErrorTimeout`** &ndash; (valor 3) Estado de error devuelto cuando la solicitud actual se ha ejecutado demasiado tiempo. Esto está pensado para evitar que los programas esperen al sensor de huellas digitales indefinidamente. El tiempo de espera es específico de la plataforma y del sensor, pero suele ser de 30 segundos.

- **`ErrorUnableToProcess`** &ndash; (valor 2) Estado de error devuelto cuando el sensor no puede procesar la imagen actual.

## <a name="related-links"></a>Vínculos relacionados

- [Cifrado](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
