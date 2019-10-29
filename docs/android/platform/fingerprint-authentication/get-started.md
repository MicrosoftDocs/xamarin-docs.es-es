---
title: Introducción con autenticación mediante huellas digitales
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020277"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Introducción con autenticación mediante huellas digitales

Para empezar, vamos a explicar primero cómo configurar un proyecto de Xamarin. Android para que la aplicación pueda usar la autenticación mediante huella digital:

1. Actualice **archivo AndroidManifest. XML** para declarar los permisos que requieren las API de huellas digitales.
2. Obtenga una referencia a la `FingerprintManager`.
3. Compruebe que el dispositivo es capaz de detectar huellas digitales.

## <a name="requesting-permissions-in-the-application-manifest"></a>Solicitar permisos en el manifiesto de aplicación

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Una aplicación de Android debe solicitar el permiso `USE_FINGERPRINT` en el manifiesto. En la captura de pantalla siguiente se muestra cómo agregar este permiso a la aplicación en Visual Studio:

[![habilitar el uso de\_huella digital en la pantalla del manifiesto de Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Una aplicación de Android debe solicitar el permiso `USE_FINGERPRINT` en el manifiesto. En la captura de pantalla siguiente se muestra cómo agregar este permiso a la aplicación en Visual Studio para Mac:

[![habilitar UseFingerprint en la pantalla de la aplicación de Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtener una instancia de FingerprintManager

A continuación, la aplicación debe obtener una instancia de la `FingerprintManager` o la clase `FingerprintManagerCompat`. Para ser compatible con versiones anteriores de Android, una aplicación de Android debe usar la API de compatibilidad que se encuentra en el paquete NuGet de compatibilidad de Android V4. En el siguiente fragmento de código se muestra cómo obtener el objeto adecuado del sistema operativo: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

En el fragmento de código anterior, el `context` es cualquier `Android.Content.Context`de Android. Normalmente, se trata de la actividad que realiza la autenticación.

## <a name="checking-for-eligibility"></a>Comprobando elegibilidad

Una aplicación debe realizar varias comprobaciones para asegurarse de que es posible usar la autenticación con huellas digitales. En total, hay cinco condiciones que la aplicación usa para comprobar la idoneidad:  

**Nivel de API 23** &ndash; las API de huellas digitales requieren el nivel de API 23 o superior. La clase `FingerprintManagerCompat` ajustará la comprobación de nivel de API. Por esta razón, se recomienda usar la **biblioteca de compatibilidad de Android V4** y `FingerprintManagerCompat`; Esto tendrá en cuenta una de estas comprobaciones.

&ndash; de **hardware** cuando la aplicación se inicia por primera vez, debe comprobar la presencia de un escáner de huellas digitales:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

El **dispositivo está protegido** &ndash; el usuario debe tener el dispositivo protegido con un bloqueo de pantalla. Si el usuario no ha protegido el dispositivo con un bloqueo de pantalla y la seguridad es importante para la aplicación, se debe notificar al usuario que debe configurarse un bloqueo de pantalla. En el fragmento de código siguiente se muestra cómo comprobar este requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

Las **huellas digitales inscritos** &ndash; el usuario deben tener al menos una huella digital registrada en el sistema operativo. Esta comprobación de permisos debe realizarse antes de cada intento de autenticación:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

Los **permisos** &ndash; la aplicación deben solicitar permiso al usuario antes de usar la aplicación. Para Android 5,0 y versiones anteriores, el usuario concede el permiso como una condición de instalación de la aplicación. Android 6,0 presentó un nuevo modelo de permiso que comprueba los permisos en tiempo de ejecución. Este fragmento de código es un ejemplo de cómo comprobar los permisos en Android 6,0:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

La comprobación de todas estas condiciones cada vez que la aplicación ofrece opciones de autenticación garantizará que el usuario obtiene la mejor experiencia de usuario. Los cambios o las actualizaciones de su dispositivo o sistema operativo pueden afectar a la disponibilidad de la autenticación mediante huella digital. Si decide almacenar en caché los resultados de cualquiera de estas comprobaciones, asegúrese de cumplir los escenarios de actualización.

Para obtener más información sobre cómo solicitar permisos en Android 6,0, consulte la guía de Android que [solicita permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Vínculos relacionados

- [Contexto](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Solicitar permisos en tiempo de ejecución](https://developer.android.com/training/permissions/requesting.html)
