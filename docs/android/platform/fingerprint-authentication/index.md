---
title: Autenticación de huella digital
description: En esta guía se describe cómo agregar la autenticación mediante huella digital, introducida en Android 6.0, a una aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: a3fd03ece3618172e68539e2a92e8924e85e2ce5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453888"
---
# <a name="fingerprint-authentication"></a>Autenticación de huella digital

_En esta guía se describe cómo agregar la autenticación mediante huella digital, introducida en Android 6.0, a una aplicación de Xamarin.Android._

## <a name="fingerprint-authentication-overview"></a>Introducción a la autenticación mediante huella digital

La llegada de escáneres de huellas digitales a los dispositivos Android proporciona a las aplicaciones una alternativa al método tradicional de nombre de usuario y contraseña para la autenticación de usuarios. El uso de huellas digitales para autenticar a un usuario permite que una aplicación incorpore una seguridad menos intrusiva que un nombre de usuario y una contraseña.

Las API de FingerprintManager están destinadas a dispositivos con un escáner de huellas digitales y ejecutan el nivel de API 23 (Android 6.0) o una versión posterior. Las API se encuentran en el espacio de nombres `Android.Hardware.Fingerprints`. La biblioteca de compatibilidad de Android v4 proporciona versiones de las API de huellas digitales diseñadas para versiones anteriores de Android. Las API de compatibilidad se encuentran en el espacio de nombres `Android.Support.v4.Hardware.Fingerprint` y se distribuyen mediante el paquete NuGet [Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (y su homólogo de la biblioteca de compatibilidad, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) es la clase principal para usar el hardware de escáner de huellas digitales. Esta clase es un contenedor de Android SDK alrededor del servicio de nivel de sistema que administra las interacciones con el propio hardware. Es el responsable de iniciar el escáner de huellas digitales y de responder a los comentarios del escáner. Esta clase tiene una interfaz bastante sencilla con solo tres miembros:

- **`Authenticate`** &ndash; Este método inicializará el escáner de hardware e iniciará el servicio en segundo plano, a la espera de que se detecte la huella digital del usuario.
- **`EnrolledFingerprints`** &ndash; Esta propiedad devolverá `true` si el usuario ha registrado una o varias huellas digitales con el dispositivo.
- **`HardwareDetected`** &ndash; Esta propiedad se usa para determinar si el dispositivo admite la detección de huellas digitales.

Una aplicación de Android usa el método `FingerprintManager.Authenticate` para iniciar el escáner de huellas digitales. El siguiente fragmento de código es un ejemplo de cómo invocarlo mediante las API de compatibilidad de la biblioteca de compatibilidad:

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

En esta guía se explica cómo usar las API de `FingerprintManager` para mejorar una aplicación de Android con autenticación mediante huellas digitales. Se explica cómo crear una instancia de `CryptoObject` y cómo crearlo para ayudar a proteger los resultados del escáner de huellas digitales. Examinaremos cómo una aplicación debe usar una subclase de `FingerprintManager.AuthenticationCallback` y responder a las indicaciones del escáner de huellas digitales. Por último, veremos cómo inscribir una huella digital en un emulador o dispositivo Android y cómo usar **adb** para simular la detección de huellas digitales.

## <a name="requirements"></a>Requisitos

La autenticación mediante huella digital requiere Android 6.0 (nivel de API 23) o superior y un dispositivo con un escáner de huellas digitales. 

Ya debe haber una huella digital inscrita en el dispositivo para cada usuario que se vaya a autenticar. Esto implica la configuración de un bloqueo de pantalla que utiliza una contraseña, un PIN, un patrón de deslizamiento o reconocimiento facial. Es posible simular parte de la funcionalidad de autenticación mediante huella digital en Android Emulator.  Para obtener más información sobre estos dos temas, consulte la sección [Inscripción de una huella digital](enrolling-fingerprint.md). 

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de la guía de huellas digitales](/samples/xamarin/monodroid-samples/fingerprintguide)
- [Ejemplo de cuadro de diálogo de huellas digitales](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Solicitar permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [API de huellas digitales y pagos (vídeo)](https://youtu.be/VOn7VrTRlA4)