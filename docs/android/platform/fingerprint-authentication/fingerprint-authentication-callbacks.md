---
title: Respuesta a las devoluciones de llamada de autenticación
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: f1fc484931ba7a574ac660b4856f20b1cb1e08a3
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119582"
---
# <a name="responding-to-authentication-callbacks"></a>Respuesta a las devoluciones de llamada de autenticación

El escáner de huellas digitales se ejecuta en segundo plano en su propio subproceso y, cuando finaliza, informará de los resultados del análisis al invocar `FingerprintManager.AuthenticationCallback` un método de en el subproceso de la interfaz de usuario. Una aplicación de Android debe proporcionar su propio controlador, que extiende esta clase abstracta, implementando todos los métodos siguientes:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Se llama cuando se produce un error irrecuperable. No hay nada más que una aplicación o usuario puede hacer para corregir la situación, salvo que es posible que vuelva a intentarlo.
- **`OnAuthenticationFailed()`** &ndash; Este método se invoca cuando se detecta una huella digital, pero el dispositivo no la reconoce.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Se llama cuando se produce un error recuperable, por ejemplo, el dedo se desliza rápidamente a través del escáner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Se llama a este método cuando se reconoce una huella digital.

Si se `CryptoObject` utilizó una al llamar `Authenticate`a, se recomienda llamar `Cipher.DoFinal` a en `OnAuthenticationSuccessful`.
`DoFinal`producirá una excepción si el cifrado se alteró o se inicializó incorrectamente, lo que indica que el resultado del escáner de huellas digitales puede haberse alterado fuera de la aplicación.


> [!NOTE]
> Se recomienda mantener la clase de devolución de llamada relativamente ligera y liberar la lógica específica de la aplicación. Las devoluciones de llamada deben actuar como un "COP de tráfico" entre la aplicación Android y los resultados del escáner de huellas digitales.

## <a name="a-sample-authentication-callback-handler"></a>Un controlador de devolución de llamada de autenticación de ejemplo

La clase siguiente es un ejemplo de una implementación `FingerprintManager.AuthenticationCallback` mínima: 

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

`OnAuthenticationSucceeded`comprueba si `Cipher` se `FingerprintManager` proporcionó cuando `Authentication` se invocó. Si es así, `DoFinal` se llama al método en el cifrado. Esto cierra `Cipher`y lo restaura a su estado original. Si se produjo un problema con el cifrado, `DoFinal` se producirá una excepción y se debe considerar que el intento de autenticación ha producido un error.

Cada una de las `OnAuthenticationError` devolucionesdellamadarecibeunenteroqueindicaelproblema.`OnAuthenticationHelp` En la siguiente sección se explica cada uno de los posibles códigos de ayuda o error. Las dos devoluciones de llamada &ndash; sirven para informar a la aplicación de que se ha producido un error en la autenticación mediante huella digital. La diferencia es la gravedad. `OnAuthenticationHelp`es un error recuperable por el usuario, como el deslizamiento de la huella digital demasiado rápido; `OnAuthenticationError` es más un error grave, como un escáner de huellas digitales dañado.

Tenga en `OnAuthenticationError` cuenta que se invocará cuando se cancele el análisis de `CancellationSignal.Cancel()` huellas digitales a través del mensaje. El `errMsgId` parámetro tendrá el valor 5 (`FingerprintState.ErrorCanceled`). Dependiendo de los requisitos, una implementación de `AuthenticationCallbacks` puede tratar esta situación de forma distinta a la de otros errores. 

`OnAuthenticationFailed`se invoca cuando la huella digital se ha analizado correctamente, pero no coincide con ninguna huella digital inscrito con el dispositivo. 

## <a name="help-codes-and-error-message-ids"></a>Códigos de ayuda e ID. de mensaje de error 

Puede encontrar una lista y una descripción de los códigos de error y los códigos de ayuda en la [documentación de Android SDK](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) de la clase FingerprintManager. Xamarin. Android representa estos valores con la `Android.Hardware.Fingerprints.FingerprintState` enumeración:


- **`AcquiredGood`** &ndash; (valor 0) la imagen adquirida fue buena.


- **`AcquiredImagerDirty`** &ndash; (valor 3) la imagen de huella digital era demasiado ruidosa debido a una suciedad sospechosa o detectada en el sensor. Por ejemplo, es razonable devolverlo después `AcquiredInsufficient` de la detección de la suciedad en el sensor (en píxeles, swaths, etc.). Se espera que el usuario tome medidas para limpiar el sensor cuando se devuelva.


- **`AcquiredInsufficient`** (valor 2) la imagen de huella digital era demasiado ruidosa para procesarse debido a una condición detectada (es decir, una máscara seca) o un sensor posiblemente sucio (consulte `AcquiredImagerDirty`. &ndash;



- **`AcquiredPartial`** &ndash; (valor 1) se ha detectado solo una imagen de huella digital parcial. Durante la inscripción, se debe informar al usuario sobre lo que debe ocurrir para resolver este problema, por ejemplo &ldquo;, al presionar firmemente en el sensor.&rdquo;



- **`AcquiredTooFast`** &ndash; (valor 5) la imagen de huella digital estaba incompleta debido al movimiento rápido. Aunque lo más adecuado para los sensores de matriz lineal, esto también podría ocurrir si el dedo se moviera durante la adquisición. Se debe solicitar al usuario que mueva el dedo más despacio (lineal) o deje el dedo en el sensor más tiempo.




- **`AcquiredToSlow`** &ndash; (valor 4) la imagen de huella digital no se pudo leer debido a la falta de movimiento. Esto es lo más adecuado para los sensores de matriz lineal que requieren un movimiento de deslizamiento.



- **`ErrorCanceled`** &ndash; (valor 5) la operación se canceló porque el sensor de huellas digitales no está disponible. Por ejemplo, esto puede ocurrir cuando se cambia el usuario, el dispositivo está bloqueado u otra operación pendiente lo impide o lo deshabilita.



- **`ErrorHwUnavailable`** &ndash; (valor 1) el hardware no está disponible. Inténtelo de nuevo más tarde.




- **`ErrorLockout`** &ndash; (valor 7) la operación se canceló porque la API está bloqueada debido a demasiados intentos.




- **`ErrorNoSpace`** &ndash; (valor 4) estado de error devuelto para operaciones como la inscripción; la operación no se puede completar porque no hay suficiente espacio de almacenamiento para completar la operación.



- **`ErrorTimeout`** &ndash; (valor 3) estado de error devuelto cuando la solicitud actual se ha ejecutado demasiado tiempo. Esto está pensado para evitar que los programas esperen el sensor de huellas digitales indefinidamente. El tiempo de espera es específico de la plataforma y del sensor, pero suele ser de 30 segundos.



- **`ErrorUnableToProcess`** &ndash; (valor 2) estado de error devuelto cuando el sensor no pudo procesar la imagen actual.



## <a name="related-links"></a>Vínculos relacionados

- [Cifra](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
