---
title: Guía de autenticación con huella digital
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027435"
---
# <a name="fingerprint-authentication-guidance"></a>Guía de autenticación con huella digital

## <a name="fingerprint-authentication-guidance"></a>Guía de autenticación con huella digital

Ahora que hemos visto los conceptos y las API relacionados con la autenticación con huella digital de Android 6.0, vamos a analizar algunos consejos generales sobre el uso de las API de huellas digitales.

1. **Usar las API de compatibilidad de la biblioteca de compatibilidad con Android v4** &ndash; Esto simplificará el código de la aplicación quitando la comprobación de la API del código y permitirá que una aplicación tenga como destino la mayoría de los dispositivos posibles.
2. **Proporcionar alternativas a la autenticación con huella digital** &ndash; La autenticación con huella digital es una forma fantástica y rápida de que una aplicación autentique a un usuario, sin embargo, no se puede suponer que siempre funcionará o estará disponible. Es posible que se produzca un error en el escáner de huellas digitales, que la lente esté sucia, que el usuario no haya configurado el dispositivo para que use la autenticación con huella digital o que las huellas digitales hayan desaparecido. También es posible que el usuario no quiera usar la autenticación con huella digital con la aplicación. Por estos motivos, una aplicación de Android debe proporcionar un proceso de autenticación alternativo, como el nombre de usuario y la contraseña.
3. **Usar el icono de huella digital de Google** &ndash; Todas las aplicaciones deben usar el mismo icono de huella digital proporcionado por Google. El uso de un icono estándar facilita que los usuarios de Android reconozcan dónde se usa la autenticación con huella digital en las aplicaciones: 
    
    ![Icono de huella digital de Android](summary-images/ic-fp-40px.png)
    
4. **Notificar al usuario** &ndash; Una aplicación debería mostrar algún tipo de notificación al usuario de que el escáner de huellas digitales está activo y esperando un toque o deslizamiento. 

## <a name="summary"></a>Resumen

La autenticación con huella digital es una excelente manera de permitir que una aplicación de Xamarin.Android compruebe rápidamente los usuarios, lo que facilita que estos interactúen con características confidenciales, como las compras en aplicación. En esta guía se describen los conceptos y el código necesarios para incorporar las API de huellas digitales de Android 6.0 en la aplicación de Xamarin.Android.

En primer lugar, analizamos las propias API de huellas digitales, `FingerprintManager` (y `FingerprintManagerCompat`). Hemos examinado el modo en que una aplicación debe extender la clase abstracta `FingerprintManager.AuthenticationCallbacks` y utilizarla como intermediario entre el hardware de huella digital y la propia aplicación. A continuación, hemos examinado cómo comprobar la integridad de los resultados del escáner de huellas digitales mediante un objeto `Cipher` de Java. Por último, hemos retocado un poco las pruebas, describiendo cómo inscribir una huella digital en un dispositivo y usando **adb** para simular un deslizamiento de huella digital en un emulador. 

Si aún no lo ha hecho, debe consultar la [aplicación de ejemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompaña a esta guía. El [ejemplo de cuadro de diálogo de huellas digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) se ha trasladado de Java a Xamarin.Android y proporciona otro ejemplo sobre cómo agregar la autenticación con huella digital a una aplicación de Android.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de la guía de huellas digitales](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icono de huella digital](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
