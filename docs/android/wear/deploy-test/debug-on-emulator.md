---
title: Depurar el desgaste de Android en un emulador
description: En estos artículos se explica cómo depurar una aplicación de desgaste de Xamarin. Android en un emulador.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: f085aaffbedb2965222b98a22cf6a4bb2393642b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764037"
---
# <a name="debug-android-wear-on-an-emulator"></a>Depurar el desgaste de Android en un emulador

_En estos artículos se explica cómo depurar una aplicación de desgaste de Xamarin. Android en un emulador._

## <a name="debug-wear-on-emulator-overview"></a>Información general sobre el desgaste en el emulador

El desarrollo de aplicaciones de uso de Android requiere la ejecución de la aplicación, ya sea en un hardware físico o en un emulador o simulador. El uso de hardware es el mejor enfoque, pero no siempre es el más práctico. En muchos casos, puede ser más sencillo y rentable simular o emular el hardware de desgaste de Android mediante un emulador, tal y como se describe a continuación. Si aún no está familiarizado con el proceso de implementación y ejecución de aplicaciones de uso de Android, vea [Hola, desgaste](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurar el Android Emulator

Para ejecutar la aplicación de desgaste en un emulador, debe instalar el Android SDK Android Emulator y configurarlo para el desgaste de Android. Para obtener información general sobre la instalación y configuración del emulador de Android SDK, consulte [Android Emulator Setup](~/android/get-started/installation/android-emulator/index.md).

Al crear un dispositivo virtual de desgaste, seleccione un perfil de dispositivo de desgaste de Android (como el cuadrado de uso de **Android**). Para mejorar el rendimiento, use el desgaste de CPU/ABI **x86** , tal como se aprecia en este ejemplo:

[![Ejemplo de configuración de dispositivo virtual de desgaste](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)

## <a name="launch-the-wear-virtual-device"></a>Inicio del dispositivo virtual de desgaste 

Después de crear un dispositivo virtual de desgaste de Android, puede elegirlo en el menú desplegable del dispositivo en el IDE antes de iniciar la depuración. Si el dispositivo virtual no está disponible en la desactivación del dispositivo, compruebe que el proyecto es un proyecto de aplicación de *desgaste* de Android (no un proyecto de aplicación de Android) y que su nivel de API de destino se establece en el mismo nivel de API que el dispositivo virtual. Por ejemplo:

[![Selección de un menú de uso de AVD en Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Una vez que se inicia el emulador de Android, Xamarin. Android implementará la aplicación de desgaste en el emulador. El emulador ejecuta la aplicación con la imagen de dispositivo virtual configurada.

No se sorprenda si ve esta (u otra pantalla intersticial) al principio. El emulador de inspección puede tardar un rato en iniciarse: 

![Ver emulador muestra solo un minuto...](debug-on-emulator-images/please-wait.png)

El emulador puede dejarse en ejecución, de modo que no es necesario cerrarlo y reiniciarlo cada vez que se ejecuta la aplicación.

## <a name="summary"></a>Resumen

En esta guía se explica cómo configurar la Android Emulator para el desarrollo de desgaste e iniciar un dispositivo virtual de desgaste para la depuración.
