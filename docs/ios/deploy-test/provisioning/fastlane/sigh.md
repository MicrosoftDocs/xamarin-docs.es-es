---
title: 'fastlane para iOS: sigh'
description: En este documento se describe el comando sigh de fastlane, que se usa para crear, renovar y reparar perfiles de aprovisionamiento para todas las configuraciones de compilación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: CD17276F-2C8C-4A46-A54C-DD532EBD5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: bb2fd504cccb7aa81831288762ba623824e52554
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526510"
---
# <a name="fastlane-for-ios-sigh"></a>fastlane para iOS: sigh

> [!IMPORTANT]
> fastlane recomienda el uso de [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) para generar y mantener los perfiles de aprovisionamiento. Use Sigh directamente solo si quiere tener un control total y sabe lo suficiente sobre la firma de código.

## <a name="overview"></a>Información general

Tradicionalmente, cada miembro de un equipo de desarrollo realiza el aprovisionamiento de dispositivos mediante Xcode o en el portal para desarrolladores de Apple. Consta de varios pasos:

- Solicitar un certificado de desarrollo
- Agregar un dispositivo al portal
- Creación de un identificador de aplicación
- Crear un perfil de aprovisionamiento
- Descargar perfiles y certificados

Cada uno de estos pasos contiene variables que deben resolverse, que dependen del tipo de aplicación que se está desarrollando. En la guía [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) (Aprovisionamiento de dispositivos) encontrará más información sobre los pasos necesarios para configurar un dispositivo para el desarrollo, ya sea manualmente o mediante Xcode.

En esta guía se presentan las herramientas de Fastlane como alternativa al uso de Xcode y se explica lo siguiente:

- [¿Qué es sigh?](#whatissigh)
- [Crear un identificador de aplicación](#appid)
- [Agregar nuevos dispositivos](#newdevices)
- [Usar sigh](#using)
- [Opciones adicionales](#options)

> [!NOTE]
> Si hay un identificador de aplicación que coincide con el identificador de agrupación de la aplicación y si el dispositivo existe en el portal para desarrolladores, puede omitir los pasos [Crear un identificador de aplicación](#appid) y [Agregar nuevos dispositivos](#newdevices). En ese caso, vaya directamente a [Usar sigh](#using) para comenzar.

## <a name="installation"></a>Instalación

Para más información sobre la instalación de fastlane, consulte la introducción de la guía de [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatissigh" />

## <a name="what-is-sigh"></a>¿Qué es sigh?

sigh proporciona una interfaz de terminal que le permite crear y renovar perfiles de aprovisionamiento para todas las configuraciones: desarrollo, distribución a través del App Store, distribución ad hoc y distribución empresarial. Además, proporciona una manera sencilla de descargar y reparar los perfiles de aprovisionamiento.

<a name="appid" />

## <a name="creating-an-app-id"></a>Creación de un identificador de aplicación

Se puede crear un identificador de aplicación con el comando siguiente:

```
fastlane produce -u your@appleid.com -a com.company.appname --skip_itc
```

Donde `com.company.appname` es el identificador de agrupación de la aplicación, que se encuentra en el archivo Info.plist de la aplicación de Xamarin.iOS, como se muestra a continuación:

[![](sigh-images/fastlane-image5.png "Archivo Info.plist de la aplicación Xamarin.iOS")](sigh-images/fastlane-image5.png#lightbox)

El identificador de aplicación único debe ser una cadena de estilo DNS inversa. Una vez creada, tome nota de ella, ya que deberá usarla cuando emplee Sigh más adelante en esta guía.

Si la aplicación tiene que crearse en [iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md), quite la marca `--skip_itc` del comando anterior.

<a name="newdevices" />

## <a name="adding-new-devices"></a>Agregar nuevos dispositivos

Para agregar un solo dispositivo en el portal para desarrolladores desde la línea de comandos, escriba el comando siguiente:

```bash
fastlane run register_device name:"Adam iPhone" udid:"abcdeg1234567"
```

Para agregar más de un dispositivo, use el `register_devices` comando:

```bash
    register_devices(
        devices: {
            "iPhone 6" => "1234567890123456789012345678901234567890",
            "iPad Air 2" => "abcdefghijklmnopqrstvuwxyzabcdefghijklmn"
         }
    )
```

<a name="using" />

## <a name="using-sigh"></a>Usar sigh

Para empezar a usar la utilidad Sigh, escriba el comando siguiente en el terminal:

```bash
fastlane sigh
```

De forma predeterminada, se creará un perfil de aprovisionamiento de [distribución en la Tienda de aplicaciones](~/ios/deploy-test/app-distribution/app-store-distribution/index.md). Para configurar el dispositivo para el desarrollo, pase la marca `--development`:

```bash
fastlane sigh --development
```

Escriba su nombre de usuario del identificador de Apple cuando Fastlane se lo solicite. Es posible que también se le pida la contraseña si es la primera vez que usa Fastlane. Si no es así, extraerá la variable de entorno de la contraseña de la cadena de claves.

Si el identificador de Apple está conectado a varios equipos, se mostrarán aquí. Seleccione el número que se corresponda con el equipo que quiera usar:

[![](sigh-images/fastlane-image2.png "Selección del equipo que se va a usar")](sigh-images/fastlane-image2.png#lightbox)

El identificador de equipo también se puede pasar a la CLI de la manera siguiente:

```bash
fastlane sigh -l 2TU993NY9J
```

Escriba el [identificador de la aplicación](#appid). Recuerde que debe coincidir con el identificador de agrupación incluido en el archivo Info.plist de la aplicación.

Todos los dispositivos conectados a la cuenta se agregarán al perfil de aprovisionamiento.

Después, fastlane creará, descargará e instalará automáticamente el perfil de aprovisionamiento.

Si busca en el Centro de desarrollo, verá el perfil de aprovisionamiento recién creado, tal como se muestra a continuación:

[![](sigh-images/fastlane-image10.png "Consulta del perfil de aprovisionamiento recién creado")](sigh-images/fastlane-image10.png#lightbox)

De forma predeterminada, sigh almacenará los perfiles de aprovisionamiento en la carpeta actual. Para cambiar el directorio de salida, edite `output_path` o haga lo siguiente:

```bash
fastlane sigh -o "~/Library/MobileDevice/Provisioning Profiles"
```

<a name="options" />

## <a name="sigh-additional-options"></a>Opciones adicionales de sigh

Las siguientes opciones se pueden usar para dar soporte técnico adicional cuando se usa Sigh:

- Para descargar todos los perfiles de aprovisionamiento, use:

    ```bash
    fastlane sigh download_all
    ```

- Para emplear una identidad de firma específica para el perfil de aprovisionamiento, use:

    ```bash
    fastlane sigh -c "Amy cert"
    ```
    
    Donde `Amy cert` es el nombre de la identidad de firma de código.


## <a name="related-links"></a>Vínculos relacionados

- [fastlane - sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme)
