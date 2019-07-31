---
title: Guía de autenticación mediante huellas digitales
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 08738a751fd630c6a413b1c7393f8007f5c97060
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643546"
---
# <a name="fingerprint-authentication-guidance"></a>Guía de autenticación mediante huellas digitales

## <a name="fingerprint-authentication-guidance"></a>Guía de autenticación mediante huellas digitales

Ahora que hemos detectado los conceptos y las API relacionados con la autenticación de huellas digitales de Android 6,0, vamos a analizar algunos consejos generales sobre el uso de las API de huellas digitales.

1. **Usar las API de compatibilidad de la biblioteca de compatibilidad de Android V4** &ndash; Esto simplificará el código de aplicación al quitar la comprobación de la API del código y permitir que una aplicación tenga como destino la mayoría de los dispositivos posibles.
2. **Proporcionar alternativas a la autenticación mediante huellas digitales** &ndash; La autenticación mediante huella digital es una forma fantástica y rápida de que una aplicación autentique a un usuario, sin embargo, no se puede suponer que siempre funcionará o estará disponible. Es posible que se produzca un error en el escáner de huellas digitales, puede que la lente se haya modificado, que el usuario no haya configurado el dispositivo para que use la autenticación mediante huella digital o que las huellas digitales no se hayan perdido. También es posible que el usuario no quiera usar la autenticación mediante huella digital con la aplicación. Por estos motivos, una aplicación Android debe proporcionar un proceso de autenticación alternativo, como el nombre de usuario y la contraseña.
3. **Usar el icono de huella digital de Google** &ndash; Todas las aplicaciones deben usar el mismo icono de huella digital proporcionado por Google. El uso de un icono estándar facilita que los usuarios de Android reconozcan dónde se usa la autenticación mediante huella digital de aplicaciones: 
    
    ![Icono de huella digital de Android](summary-images/ic-fp-40px.png)
    
4. **Notificar al usuario** &ndash; Una aplicación debe mostrar algún tipo de notificación al usuario de que el escáner de huellas digitales esté activo y esperar a tocar o deslizar rápidamente. 

## <a name="summary"></a>Resumen

La autenticación mediante huella digital es una excelente manera de permitir que una aplicación de Xamarin. Android Compruebe rápidamente a los usuarios, lo que facilita a los usuarios la interacción con características confidenciales como las compras desde la aplicación. En esta guía se describen los conceptos y el código necesarios para incorporar las API de huellas digitales de Android 6,0 en la aplicación Xamarin. Android.

En primer lugar, analizamos las propias API `FingerprintManager` de huellas `FingerprintManagerCompat`digitales, (y). Hemos examinado cómo `FingerprintManager.AuthenticationCallbacks` una aplicación debe extender la clase abstracta y utilizarla como intermediario entre el hardware de huella digital y la propia aplicación. Después, hemos examinado cómo comprobar la integridad de los resultados del escáner de huellas `Cipher` digitales con un objeto de Java. Por último, hemos retocado un poco en las pruebas describiendo cómo inscribir una huella digital en un dispositivo y usar **ADB** para simular una huella digital en un emulador. 

Si todavía no lo ha hecho, debe examinar la aplicación de [ejemplo](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) que acompaña a esta guía. El [ejemplo de cuadro de diálogo de huellas digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) se ha trasladado de Java a Xamarin. Android y proporciona otro ejemplo sobre cómo agregar la autenticación mediante huellas digitales a una aplicación de Android.



## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de guía de huellas digitales](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Ejemplo de cuadro de diálogo de huellas digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icono de huella digital](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
