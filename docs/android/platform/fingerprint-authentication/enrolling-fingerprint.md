---
title: Inscripción de una huella digital
description: Cómo configurar un bloqueo de pantalla e inscribir una huella digital en un dispositivo o emulador Android.
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027594"
---
# <a name="enrolling-a-fingerprint"></a>Inscripción de una huella digital

## <a name="enrolling-a-fingerprint-overview"></a>Introducción a la inscripción de huellas digitales

Solo es posible que una aplicación Android aproveche la autenticación mediante huella digital si el dispositivo ya se ha configurado con autenticación mediante huellas digitales. En esta guía se explica cómo inscribir una huella digital en un dispositivo o emulador Android. Los emuladores no tienen el hardware real para realizar un examen de huellas digitales, pero es posible simular un examen de huellas digitales con la ayuda del Android Debug Bridge (que se describe a continuación).  En esta guía se explica cómo habilitar el bloqueo de pantalla en un dispositivo Android e inscribir una huella digital para la autenticación.

## <a name="requirements"></a>Requisitos

Para inscribir una huella digital, debe tener un dispositivo Android o un emulador que ejecute el nivel de API 23 (Android 6,0).

El uso del Android Debug Bridge (ADB) requiere que se familiarice con el símbolo del sistema y el ejecutable de **ADB** debe estar en la ruta de acceso del entorno de Bash, PowerShell o del símbolo del sistema.

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>Configuración de un bloqueo de pantalla e inscripción de una huella digital 

Para configurar un bloqueo de pantalla, realice los pasos siguientes:

1. Vaya a **configuración > seguridad**y seleccione **bloqueo de pantalla**:

    ![Ubicación de la selección del bloqueo de pantalla en la pantalla de seguridad](enrolling-fingerprint-images/testing-01.png)

2. La siguiente pantalla que aparece le permitirá seleccionar y configurar uno de los métodos de seguridad de bloqueo de pantalla: 

    ![Seleccionar el dedo, el patrón, el PIN o la contraseña](enrolling-fingerprint-images/testing-02.png)

   Seleccione y complete uno de los métodos de bloqueo de pantalla disponibles.

3. Una vez configurada la screenlock, vuelva a la página **configuración > seguridad** y seleccione **huella digital**:

    ![Ubicación de la selección de huella digital en la pantalla de seguridad](enrolling-fingerprint-images/testing-03.png)

4. Desde allí, siga la secuencia para agregar una huella digital al dispositivo:

    [![secuencia de capturas de pantallas para agregar una huella digital al dispositivo](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. En la última pantalla se le pedirá que coloque el dedo en el escáner de huellas digitales: 

    ![Pantalla que le pide que coloque el dedo en el sensor](enrolling-fingerprint-images/testing-05.png)

    Si usa un dispositivo Android, rellene un dedo al escáner para completar el proceso. 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>Simular un examen de huellas digitales en el emulador

En un emulador de Android, es posible simular un examen de huellas digitales mediante el Android Debug Bridge. En OS X iniciar una sesión de terminal mientras en Windows inicia un símbolo del sistema o una sesión de PowerShell y ejecuta `adb`:

```shell
$ adb -e emu finger touch 1
```

El valor **1** es el _dedo\_identificador_ del dedo que se "examinó". Es un entero único que se asigna para cada huella digital virtual. En el futuro en el que se ejecuta la aplicación, puede ejecutar este mismo comando ADB cada vez que el emulador le pida una huella digital, puede ejecutar el comando `adb` y pasarle el _dedo\_identificador_ para simular el análisis de huellas digitales.

Una vez completado el examen de huellas digitales, Android le notificará que se ha agregado la huella digital:  

![Pantalla que muestra la huella digital agregada.](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>Resumen 

En esta guía se explica cómo configurar un bloqueo de pantalla e inscribir una huella digital en un dispositivo Android o en un emulador de Android. 
