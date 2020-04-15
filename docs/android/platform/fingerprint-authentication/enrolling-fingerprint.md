---
title: Inscripción de una huella digital
description: Configuración de un bloqueo de pantalla e inscripción de una huella digital en un emulador o dispositivo Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027594"
---
# <a name="enrolling-a-fingerprint"></a>Inscripción de una huella digital

## <a name="enrolling-a-fingerprint-overview"></a>Introducción a la inscripción de huellas digitales

Una aplicación para Android solo puede utilizar la autenticación mediante huella digital si el dispositivo ya se ha configurado con la autenticación mediante huellas digitales. En esta guía se describe cómo inscribir una huella digital en un emulador o dispositivo Android. Los emuladores no tienen el hardware real para la detección de huellas digitales, pero esta se puede simular con la ayuda de Android Debug Bridge (se describe a continuación).  En esta guía se explica cómo habilitar el bloqueo de pantalla en un dispositivo Android e inscribir una huella digital para la autenticación.

## <a name="requirements"></a>Requisitos

Para inscribir una huella digital, debe tener un dispositivo Android o un emulador que ejecute el nivel de API 23 (Android 6.0).

El uso de Android Debug Bridge (ADB) requiere estar familiarizado con el símbolo del sistema y el archivo ejecutable **adb** debe estar en la ruta de acceso del entorno de Bash, PowerShell o el símbolo del sistema.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configuración de un bloqueo de pantalla e inscripción de una huella digital 

Siga los pasos que se describen a continuación para configurar un bloqueo de pantalla:

1. Vaya a **Configuración > Seguridad** y seleccione **Bloqueo de pantalla**:

    ![Ubicación de la selección del bloqueo de pantalla en la pantalla de seguridad](enrolling-fingerprint-images/testing-01.png)

2. La siguiente pantalla que aparece le permitirá seleccionar y configurar uno de los métodos de seguridad de bloqueo de pantalla: 

    ![Seleccione la opción de deslizamiento, patrón, PIN o contraseña](enrolling-fingerprint-images/testing-02.png)

   Seleccione y complete uno de los métodos de bloqueo de pantalla disponibles.

3. Una vez configurado el bloqueo de pantalla, vuelva a la página **Configuración > Seguridad** y seleccione **Huella digital**:

    ![Ubicación de la selección de huella digital en la pantalla de seguridad](enrolling-fingerprint-images/testing-03.png)

4. Desde allí, siga la secuencia para agregar una huella digital al dispositivo:

    [![Secuencia de capturas de pantalla para agregar una huella digital al dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. En la última pantalla, se le pedirá que coloque el dedo en el escáner de huellas digitales: 

    ![Mensaje que le pide que coloque el dedo en el sensor](enrolling-fingerprint-images/testing-05.png)

    Si usa un dispositivo Android, coloque el dedo sobre el escáner para completar el proceso. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simulación de detección de huellas digitales en el emulador

En un emulador de Android, es posible simular una detección de huellas digitales mediante Android Debug Bridge. En OS X, inicie una sesión de terminal. O bien, en Windows, inicie un símbolo del sistema o una sesión de PowerShell y ejecute `adb`:

```shell
$ adb -e emu finger touch 1
```

El valor de **1** representa un dedo "detectado" como _finger\_id_. Es un entero único que se asigna a cada huella digital virtual. En el futuro, cuando se ejecute la aplicación, puede ejecutar este mismo comando de ADB cada vez que el emulador le pida una huella digital, puede ejecutar el comando `adb` y pasarle el valor de _finger\_id_ para simular la detección de huellas digitales.

Una vez completada la detección de huellas digitales, Android le notificará que se ha agregado la huella digital:  

![Pantalla que muestra que la huella digital se ha agregado.](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumen 

En esta guía se ha cubierto cómo configurar un bloqueo de pantalla e inscribir una huella digital en un emulador o dispositivo Android. 
