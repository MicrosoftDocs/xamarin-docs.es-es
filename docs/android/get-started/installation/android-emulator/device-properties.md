---
title: Editar las propiedades del dispositivo virtual Android
description: En este artículo se describe cómo usar Android Device Manager para editar las propiedades de perfil de un dispositivo virtual Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 3E33C136-8042-4184-A40C-3200D8CD99CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: ab4e0e79c39d36adcfb29e9659c2c6a924c80470
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020871"
---
# <a name="editing-android-virtual-device-properties"></a>Editar las propiedades del dispositivo virtual Android

_En este artículo se describe cómo usar Android Device Manager para editar las propiedades de perfil de un dispositivo virtual Android._

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager en Windows

**Android Device Manager** es compatible con la edición de propiedades de perfil de dispositivos virtuales Android individuales. En las pantallas **Nuevo dispositivo** y **Edición de dispositivos** se muestran las propiedades del dispositivo virtual en la primera columna, con los valores correspondientes de cada propiedad en la segunda columna (como se ve en este ejemplo): 

[![Pantalla de ejemplo de Nuevo dispositivo](device-properties-images/win/01-new-device-editor-sml.png)](device-properties-images/win/01-new-device-editor.png#lightbox)

Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha. Puede modificar las *propiedades de perfil de hardware* y las *propiedades de AVD*. Las propiedades de perfil de hardware (como `hw.ramSize` y `hw.accelerometer`) describen las características físicas del dispositivo emulado. Estas características incluyen el tamaño de pantalla, la cantidad de memoria RAM disponible y si hay un acelerómetro o no. Las propiedades de AVD especifican el funcionamiento del AVD durante su ejecución. Por ejemplo, se pueden configurar las propiedades de AVD para especificar la forma en que el AVD usa la tarjeta gráfica del equipo de desarrollo para la representación.

Puede cambiar las propiedades de la manera siguiente:

- Para cambiar una propiedad booleana, haga clic en la marca de verificación situada a la derecha de la propiedad booleana:

    ![Cambiar una propiedad booleana](device-properties-images/win/02-boolean-value.png)

- Para cambiar una propiedad *enum* (enumerada), haga clic en la flecha abajo situada a la derecha de la propiedad y seleccione un nuevo valor.

    ![Cambiar una propiedad enum](device-properties-images/win/04-enum-value.png)

- Para cambiar una propiedad de cadena o entero, haga doble clic en la configuración actual de la cadena o del entero en la columna de valor y especifique un nuevo valor.

    ![Cambiar una propiedad de entero](device-properties-images/win/03-integer-value.png)

::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager en macOS

**Android Device Manager** es compatible con la edición de propiedades de perfil de dispositivos virtuales Android individuales. En las pantallas **Nuevo dispositivo** y **Edición de dispositivos** se muestran las propiedades del dispositivo virtual en la primera columna, con los valores correspondientes de cada propiedad en la segunda columna (como se ve en este ejemplo): 

[![Pantalla de ejemplo de Nuevo dispositivo](device-properties-images/mac/01-new-device-editor-sml.png)](device-properties-images/mac/01-new-device-editor.png#lightbox)

Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha. Puede modificar las *propiedades de perfil de hardware* y las *propiedades de AVD*. Las propiedades de perfil de hardware (como `hw.ramSize` y `hw.accelerometer`) describen las características físicas del dispositivo emulado. Estas características incluyen el tamaño de pantalla, la cantidad de memoria RAM disponible y si hay un acelerómetro o no. Las propiedades de AVD especifican el funcionamiento del AVD durante su ejecución. Por ejemplo, se pueden configurar las propiedades de AVD para especificar la forma en que el AVD usa la tarjeta gráfica del equipo de desarrollo para la representación.

Puede cambiar las propiedades de la manera siguiente:

- Para cambiar una propiedad booleana, haga clic en la marca de verificación situada a la derecha de la propiedad booleana:

    ![Cambiar una propiedad booleana](device-properties-images/mac/02-boolean-value.png)

- Para cambiar una propiedad *enum* (enumerada), haga clic en el menú desplegable situado a la derecha de la propiedad y seleccione un nuevo valor.

    ![Cambiar una propiedad enum](device-properties-images/mac/04-enum-value.png)

- Para cambiar una propiedad de cadena o entero, haga doble clic en la configuración actual de la cadena o del entero en la columna de valor y especifique un nuevo valor.

    ![Cambiar una propiedad de entero](device-properties-images/mac/03-integer-value.png)

::: zone-end

En la tabla siguiente se proporciona una explicación detallada de las propiedades enumeradas en las pantallas **Nuevo dispositivo** y **Edición de dispositivos**:

[!include[](~/android/includes/emulator-properties.md)]

Para más información sobre estas propiedades, vea [Propiedades de perfil de hardware](https://developer.android.com/studio/run/managing-avds.html#hpproperties).
