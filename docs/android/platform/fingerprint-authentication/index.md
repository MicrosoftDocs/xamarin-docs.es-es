---
title: Autenticación de huella digital
description: En esta guía se describe cómo agregar la autenticación mediante huella digital, introducida en Android 6,0, a una aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: ac373d20cbe08719fd7aa8e3888bbd608d8549c7
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119522"
---
# <a name="fingerprint-authentication"></a>Autenticación de huella digital

_En esta guía se describe cómo agregar la autenticación mediante huella digital, introducida en Android 6,0, a una aplicación de Xamarin. Android._


## <a name="fingerprint-authentication-overview"></a>Introducción a la autenticación mediante huellas digitales

La llegada de escáneres de huellas digitales en dispositivos Android proporciona a las aplicaciones una alternativa al método tradicional de nombre de usuario y contraseña de autenticación de usuarios. El uso de huellas digitales para autenticar a un usuario permite que una aplicación incorpore seguridad menos intrusiva que un nombre de usuario y una contraseña.

Las API de FingerprintManager tienen como destino dispositivos con un escáner de huellas digitales y ejecutan el nivel de API 23 (Android 6,0) o una versión posterior. Las API se encuentran en el `Android.Hardware.Fingerprints` espacio de nombres. La biblioteca de compatibilidad de Android V4 proporciona versiones de las API de huellas digitales diseñadas para versiones anteriores de Android. Las API de compatibilidad se encuentran en `Android.Support.v4.Hardware.Fingerprint` el espacio de nombres, se distribuyen mediante el [paquete NuGet Xamarin. Android. support. V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (y su homólogo de la biblioteca de compatibilidad, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) es la clase principal para usar el hardware de análisis de huellas digitales. Esta clase es un contenedor de Android SDK alrededor del servicio de nivel de sistema que administra las interacciones con el propio hardware. Es responsable de iniciar el escáner de huellas digitales y de responder a los comentarios del escáner. Esta clase tiene una interfaz bastante sencilla con solo tres miembros:

- **`Authenticate`** &ndash; Este método inicializará el explorador de hardware e iniciará el servicio en segundo plano, esperando a que el usuario digitalice su huella digital.
- **`EnrolledFingerprints`** Esta propiedad devolverá `true` si el usuario ha registrado una o varias huellas digitales con el dispositivo. &ndash;
- **`HardwareDetected`** &ndash; Esta propiedad se usa para determinar si el dispositivo admite el análisis de huellas digitales.

Una `FingerprintManager.Authenticate` aplicación de Android usa el método para iniciar el escáner de huellas digitales. El siguiente fragmento de código es un ejemplo de cómo invocarlo mediante las API de compatibilidad de la biblioteca de soporte:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

En esta guía se explica cómo usar las `FingerprintManager` API para mejorar una aplicación de Android con autenticación mediante huellas digitales. Se explica cómo crear una instancia de y crear un `CryptoObject` para ayudar a proteger los resultados del escáner de huellas digitales. Examinaremos el modo en que una aplicación debe `FingerprintManager.AuthenticationCallback` subclase y responder a los comentarios del escáner de huellas digitales. Por último, veremos cómo inscribir una huella digital en un emulador o dispositivo Android y cómo usar **ADB** para simular un análisis de huellas digitales.

## <a name="requirements"></a>Requisitos

La autenticación mediante huella digital requiere Android 6,0 (nivel de API 23) o superior y un dispositivo con un escáner de huellas digitales. 

Una huella digital ya debe estar inscrita en el dispositivo para cada usuario que se va a autenticar. Esto implica la configuración de un bloqueo de pantalla que utiliza una contraseña, un PIN, un patrón de deslizamiento o reconocimiento facial. Es posible simular parte de la funcionalidad de autenticación mediante huella digital en un Android Emulator.  Para obtener más información sobre estos dos temas, consulte la sección [inscripción de una huella digital](enrolling-fingerprint.md) . 






## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de guía de huellas digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Solicitar permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [API de huellas digitales y pagos (vídeo)](https://youtu.be/VOn7VrTRlA4)
