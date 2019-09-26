---
title: Características de seguridad y privacidad de iOS
description: En este documento se describen las características de seguridad y privacidad de iOS y se explica cómo usarlas con Xamarin. iOS. Se examinan las actualizaciones realizadas en iOS 10 y cómo obtener acceso a los datos de usuario privados.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 7847148551c20dbcf49bcc263bdc50716a6ef14e
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "70283171"
---
# <a name="ios-security-and-privacy-features"></a>Características de seguridad y privacidad de iOS

_En este artículo se explica cómo trabajar con la seguridad y la privacidad en iOS y cómo afectan a una aplicación de Xamarin. iOS._

Apple ha realizado varias mejoras en la seguridad y la privacidad en iOS 10 (y versiones posteriores) que ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y a garantizar la privacidad del usuario final. En este artículo se trata la implementación de estas características en una aplicación Xamarin. iOS.

<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Mejoras generales

Se han realizado los siguientes cambios generales en la seguridad y la privacidad en iOS 10:

- La API de Common Data Security Architecture (CDSA) ha quedado en desuso y debe reemplazarse por la API de SecKey para generar claves asimétricas.
- La nueva `NSAllowsArbitraryLoadsInWebContent` clave se puede Agregar al archivo **info. plist** de la aplicación y permitirá que las páginas web se carguen correctamente, mientras que la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación. Para obtener más información, consulte nuestra documentación de [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) .
- Dado que el nuevo portapapeles en iOS 10 y macOS Sierra permite al usuario copiar y pegar entre dispositivos, la API se ha expandido para permitir que un portapapeles se limite a un dispositivo específico y que tenga la marca de tiempo para que se borre automáticamente en un momento dado. Además, los portapapeles con nombre ya no se conservan y deben reemplazarse por los contenedores de la mesa de pegada.
- Para todas las conexiones SSL/TLS, el cifrado simétrico RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que el desarrollador deje de usar la criptografía SHA-1 y 3DES lo antes posible.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Acceso a datos de usuario privados

Las aplicaciones que se ejecutan en iOS 10 (o posterior) deben declarar estáticamente su intención de acceder a características específicas o información de usuario especificando una o varias claves de privacidad en sus archivos **info. plist** , que explican al usuario por qué la aplicación quiere obtener acceso.

> [!IMPORTANT]
> El sistema terminará de forma silenciosa las aplicaciones que no proporcionen las claves necesarias cuando intenten acceder a una de las características restringidas o a la información de usuario, _sin errores_. Si una aplicación se inicia de forma inesperada en iOS 10, asegúrese de que se ha especificado toda la información requerida. **plist** .

Están disponibles las siguientes claves relacionadas con la privacidad:

- **Privacidad: Descripción de uso de Apple Music** (`NSAppleMusicUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca multimedia del usuario.
- **Privacidad: Descripción de uso de periféricos Bluetooth** (`NSBluetoothPeripheralUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere acceder a Bluetooth en el dispositivo del usuario.
- **Privacidad: Descripción de uso de calendarios** (`NSCalendarsUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso al calendario del usuario.
- **Privacidad: Descripción de uso de la cámara** (`NSCameraUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere tener acceso a la cámara del dispositivo.
- **Privacidad-Descripción del uso de contactos** (`NSContactsUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso a los contactos del usuario.
- **Privacidad: Descripción del uso de recursos compartidos de mantenimiento** (`NSHealthShareUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso a los datos de mantenimiento del usuario. Para obtener más información, consulte referencia de la [clase HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)de Apple.
- **Privacidad: Descripción de uso de actualización de estado** (`NSHealthUpdateUsageDescription`): Permite al desarrollador describir por qué la aplicación desea editar los datos de estado del usuario. Para obtener más información, consulte referencia de la [clase HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)de Apple.
- **Privacidad: Descripción de uso de HomeKit** (`NSHomeKitUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere acceder a los datos de configuración de HomeKit del usuario.
- **Privacidad: Descripción de uso de la ubicación siempre** (`NSLocationAlwaysUsageDescription`): Permite al desarrollador describir por qué la aplicación desea siempre tener acceso a la ubicación del usuario.
- En desuso **Privacidad: Descripción de uso de ubicación** (`NSLocationUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso a la ubicación del usuario. *NOTA: Esta clave está en desuso en iOS 8 (y versiones posteriores). Use `NSLocationAlwaysUsageDescription` o`NSLocationWhenInUseUsageDescription` en su lugar.*
- **Privacidad: Descripción de uso de la ubicación en** uso (`NSLocationWhenInUseUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere tener acceso a la ubicación del usuario mientras se ejecuta.
- En desuso **Privacidad: Descripción de uso de la biblioteca multimedia** : permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca multimedia del usuario. *NOTA: Esta clave está en desuso en iOS 8 (y versiones posteriores). Use `NSAppleMusicUsageDescription` en su lugar.*
- **Privacidad: Descripción de uso del micrófono** (`NSMicrophoneUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso al micrófono de los dispositivos.
- **Privacidad: Descripción del uso de movimiento** (`NSMotionUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere acceder al acelerómetro del dispositivo.
- **Privacidad: Descripción de uso de la biblioteca de fotos** (`NSPhotoLibraryUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca de fotos del usuario.
- **Privacidad: Descripción de uso de recordatorios** (`NSRemindersUsageDescription`): Permite al desarrollador describir por qué la aplicación desea tener acceso a los recordatorios del usuario.
- **Privacidad: Descripción de uso de Siri** (`NSSiriUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere enviar datos de usuario a Siri.
- **Privacidad: Descripción de uso del reconocimiento de voz** (`NSSpeechRecognitionUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere enviar datos de usuario a los servidores de reconocimiento de voz de Apple.
- **Privacidad: Descripción del uso del proveedor de TV** (`NSVideoSubscriberAccountUsageDescription`): Permite al desarrollador describir por qué la aplicación quiere acceder a la cuenta de proveedor de TV del usuario.

Para obtener más información sobre cómo trabajar con las claves **info. plist** , consulte la [referencia de clave](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)de la lista de propiedades de la información de Apple.

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Configuración de las claves de privacidad

En el siguiente ejemplo de acceso a HomeKit en iOS 10 (y versiones posteriores), el desarrollador tendrá que agregar la `NSHomeKitUsageDescription` clave al archivo **info. plist** de la aplicación y proporcionar una cadena que declare por qué la aplicación desea tener acceso a la base de datos HomeKit del usuario. Esta cadena se presentará al usuario la primera vez que ejecute la aplicación:

![Una alerta de NSHomeKitUsageDescription de ejemplo](security-privacy-images/info01.png "Una alerta de NSHomeKitUsageDescription de ejemplo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin. iOS para Visual Studio no admite actualmente la edición de las claves de privacidad **info. plist** desde el editor de manifiestos de iOS predeterminado. En su lugar, tendrá que usar el editor de plist genérico, por lo que debe hacer lo siguiente:

1. Haga clic con el botón derecho en el archivo **info. plist** en el **Explorador de soluciones** y seleccione **abrir con.** ...
2. Seleccione el **Editor de plist genérico** en la lista de programas para abrir el archivo y, a continuación, haga clic en **Aceptar**.

    ![Seleccione el editor de plist genérico](security-privacy-images/InfoEditorSelectionVs.png "Seleccione el editor de plist genérico")
3. Haga clic **+** en el botón de la última fila del editor para agregar una nueva entrada a la lista. Esto se denominará "propiedad personalizada", con el tipo establecido en `String` y un valor vacío.
4. Haga clic en el nombre de la propiedad y aparecerá una lista desplegable.
5. En la lista desplegable, seleccione una clave de privacidad (como **privacidad-HomeKit Usage Description**): 

    ![Seleccionar una clave de privacidad](security-privacy-images/InfoPListEditorSelectKey.png "Seleccionar una clave de privacidad")
6. Escriba una descripción en la columna valor por el motivo por el que la aplicación desea tener acceso a la información de usuario o característica determinada: 

    ![Escriba una descripción](security-privacy-images/InfoPListSetValue.png "Escriba una descripción")
7. Guarde los cambios en el archivo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para establecer cualquiera de las claves de privacidad, haga lo siguiente:

1. Haga doble clic en el archivo **Info.plist**, en el **Explorador de soluciones**, para abrirlo para su edición.
2. En la parte inferior de la pantalla, cambie a la vista **código fuente** .
3. Agregar una nueva **entrada** a la lista.
4. En la lista desplegable, seleccione una clave de privacidad (como **privacidad-HomeKit Usage Description**): 

    ![Seleccionar una clave de privacidad](security-privacy-images/info02.png "Seleccionar una clave de privacidad")
5. Escriba una descripción de por qué la aplicación desea tener acceso a la información de usuario o característica determinada: 

    ![Escriba una descripción](security-privacy-images/info03.png "Escriba una descripción")
6. Guarde los cambios en el archivo.

-----

> [!IMPORTANT]
> En el ejemplo anterior, si no se establece la `NSHomeKitUsageDescription` clave en el archivo **info. plist** , se _producirá un_ error en la aplicación en modo silencioso (cerrado por el sistema en tiempo de ejecución) sin errores cuando se ejecute en iOS 10 (o superior).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se han tratado los cambios de seguridad y privacidad que Apple ha realizado en iOS 10 y cómo afectan a una aplicación de Xamarin. iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
