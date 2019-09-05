---
title: Administración de dispositivos virtuales con Android Device Manager
description: En este artículo se explica cómo usar Android Device Manager para crear y configurar dispositivos virtuales Android (AVD) que emulen dispositivos Android físicos. Puede usar estos dispositivos virtuales para ejecutar y probar la aplicación sin tener que depender de un dispositivo físico.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 200ee3fdee3274063becec19fbc6d1504e132a88
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226641"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>Administración de dispositivos virtuales con Android Device Manager

_En este artículo se explica cómo usar Android Device Manager para crear y configurar dispositivos virtuales Android (AVD) que emulen dispositivos Android físicos. Puede usar estos dispositivos virtuales para ejecutar y probar la aplicación sin tener que depender de un dispositivo físico._

Después de comprobar que la aceleración de hardware está habilitada (como se explica en [Aceleración de hardware para el rendimiento del emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)), el siguiente paso es usar _Android Device Manager_ (también denominado _Xamarin Android Device Manager_) para crear dispositivos virtuales que se puedan usar para probar y depurar la aplicación.

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Android Device Manager en Windows

En este artículo se explica cómo se usa Android Device Manager para crear, duplicar, personalizar e iniciar dispositivos virtuales Android.

[![Captura de pantalla de Android Device Manager en la pestaña Dispositivos](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

Android Device Manager se usa para crear y configurar _dispositivos virtuales Android_ (AVD) que se ejecutan en [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD es una configuración del emulador que simula un dispositivo Android físico. Esto permite ejecutar y probar la aplicación en diversas configuraciones que simulan diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

Para usar Android Device Manager, necesita los siguiente elementos:

- Visual Studio 2019 Community, Professional o Enterprise.

- O bien, se necesita Visual Studio 2017 versión 15.8 o posterior. Se admiten las ediciones Community, Professional y Enterprise de Visual Studio.

- Visual Studio Tools para Xamarin versión 4.9 o posterior.

- Debe estar instalado Android SDK (consulte [Configuración de Android SDK para Xamarin.Android](~/android/get-started/installation/android-sdk.md)).
  Asegúrese de instalar Android SDK en la ubicación predeterminada (si todavía no lo está): **C:\\Archivos de programa (x86)\\Android\\android-sdk**.

- Es necesario instalar los siguientes paquetes (a través de [Android SDK Manager](~/android/get-started/installation/android-sdk.md)): 
  - **Android SDK Tools versión 26.1.1** o posterior
  - **Herramientas de plataforma de Android SDK 27.0.1** o posterior
  - **Herramientas de compilación de Android SDK 27.0.3** o posterior 
  - **Android Emulator 27.2.7** o posterior. 

  Estos paquetes deberían mostrarse con el estado **Instalado**, como se muestra en la captura de pantalla siguiente:

  [![Instalación de Android SDK Tools](device-manager-images/win/02-sdk-tools-sml.png)](device-manager-images/win/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>Iniciar el Administrador de dispositivos

Inicie Android Device Manager desde el menú **Herramientas**. Para ello, haga clic en **Herramientas > Android > Android Device Manager**:

[![Inicio del administrador de dispositivos desde el menú Herramientas](device-manager-images/win/03-tools-menu-sml.png)](device-manager-images/win/03-tools-menu.png#lightbox)

Si aparece el siguiente cuadro de diálogo de error al inicio, consulte la sección [Solución de problemas](#troubleshooting) para obtener soluciones alternativas:

![Cuadro de diálogo de error de instancia de Android SDK](device-manager-images/win/04-sdk-error.png)


## <a name="main-screen"></a>Pantalla principal

Cuando inicie el Administrador de dispositivos Android por primera vez, aparecerá una pantalla con todos los dispositivos virtuales configurados. Para cada dispositivo virtual, se muestran el **nombre**, el **SO** (versión de Android), el **procesador**, el tamaño de **memoria** y la **resolución** de pantalla:

[![Lista de los dispositivos instalados y sus parámetros](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

Al seleccionar un dispositivo de la lista, aparece el botón **Iniciar** a la derecha. Puede hacer clic en el botón **Iniciar** para iniciar el emulador con este dispositivo virtual:

[![Botón Iniciar de una imagen de dispositivo](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

Una vez que el emulador se haya iniciado con el dispositivo virtual seleccionado, el botón **Iniciar** cambiará al botón **Detener**, que puede usar para detener el emulador:

[![Botón Detener del dispositivo en ejecución](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>Dispositivo nuevo

Para crear un dispositivo, haga clic en el botón **Nuevo** (situado en la parte superior derecha de la pantalla):

[![Botón Nuevo para crear un dispositivo](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

Al hacer clic en **Nuevo**, aparece la pantalla **Nuevo dispositivo**:

[![Pantalla Nuevo dispositivo del Administrador de dispositivos](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

Para configurar un nuevo dispositivo en la pantalla **Nuevo dispositivo**, siga estos pasos:

1. Asigne un nuevo nombre al dispositivo. En el ejemplo siguiente, el nuevo dispositivo se denomina **Pixel_API_27**:

   [![Asignar un nombre al nuevo dispositivo](device-manager-images/win/10-device-name-sml.png)](device-manager-images/win/10-device-name.png#lightbox)

2. Seleccione el dispositivo físico que quiera emular; para ello, haga clic en el menú desplegable **Dispositivo base**:

   [![Seleccionar el dispositivo físico que se va a emular](device-manager-images/win/11-device-menu-sml.png)](device-manager-images/win/11-device-menu.png#lightbox)

3. Seleccione un tipo de procesador para este dispositivo virtual; para ello, haga clic en el menú desplegable **Procesador**. Si selecciona **x86**, se proporcionará el mejor rendimiento porque permite que el emulador aproveche las ventajas de la [aceleración de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   La opción **x86_64** también usará la aceleración de hardware, pero la ejecución es un poco más lenta que **x86** (**x86_64** se suele usar para probar aplicaciones de 64 bits):

   [![Seleccionar el tipo de procesador](device-manager-images/win/12-processor-type-menu-sml.png)](device-manager-images/win/12-processor-type-menu.png#lightbox)

4. Seleccione la versión de Android (nivel de API); para ello, haga clic en el menú desplegable **SO**. Por ejemplo, seleccione **Oreo 8.1 - API 27** para crear un dispositivo virtual de nivel de API 27:

   [![Seleccionar la versión de Android](device-manager-images/win/13-android-version-w158-sml.png)](device-manager-images/win/13-android-version-w158.png#lightbox)

   Si selecciona un nivel de API de Android que aún no se ha instalado, Device Manager mostrará el mensaje **A new device will be downloaded** (Se descargará un nuevo dispositivo) en la parte inferior de la pantalla y descargará e instalará los archivos necesarios, además de crear el dispositivo virtual:

   ![Se descargará una nueva imagen de dispositivo](device-manager-images/win/14-automatic-download-w158.png)

5. Si quiere incluir las API de Google Play Services en el dispositivo virtual, habilite la opción **Google APIs** (API de Google). Para incluir la aplicación de Google Play Store, habilite la opción **Google Play Store**:

   [![Seleccionar Google Play Services y Google Play Store](device-manager-images/win/15-google-play-services-sml.png)](device-manager-images/win/15-google-play-services.png#lightbox)

   Tenga en cuenta que las imágenes de Google Play Store solo están disponibles para algunos tipos de dispositivos base como Pixel, Pixel 2, Nexus 5 y Nexus 5X.

6. Edite las propiedades que necesite modificar. Para realizar cambios en las propiedades, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).

7. Agregue las propiedades adicionales que necesite establecer explícitamente. En la pantalla **Nuevo dispositivo** solo se muestran las propiedades que se modifican con más frecuencia, pero puede hacer clic en el menú desplegable **Agregar propiedad** (en la parte inferior) para agregar propiedades adicionales:

   [![Menú desplegable Agregar propiedad](device-manager-images/win/16-add-property-menu-sml.png)](device-manager-images/win/16-add-property-menu.png#lightbox)

    También puede definir una propiedad personalizada si selecciona **Personalizado...** en la parte superior de la lista de propiedades.

8. Haga clic en el botón **Crear** (esquina inferior derecha) para crear el dispositivo:

   [![Botón Crear](device-manager-images/win/17-create-button-sml.png)](device-manager-images/win/17-create-button.png#lightbox)

9. Es posible que aparezca la pantalla **Aceptación de licencia**. Haga clic en **Aceptar** si está de acuerdo con los términos de licencia:

   [![Pantalla de aceptación de licencia](device-manager-images/win/18-license-acceptance-sml.png)](device-manager-images/win/18-license-acceptance.png#lightbox)

10. Android Device Manager agrega el nuevo dispositivo a la lista de dispositivos virtuales instalados, con el indicador de progreso **Creando** mientras crea el dispositivo:

    [![Indicador de progreso de creación](device-manager-images/win/19-creating-the-device-sml.png)](device-manager-images/win/19-creating-the-device.png#lightbox)

11. Una vez finalizado el proceso de creación, el nuevo dispositivo se muestra en la lista de dispositivos virtuales instalados con el botón **Iniciar**, listo para su inicio:

    [![Dispositivo recién creado listo para su inicio](device-manager-images/win/20-created-device-sml.png)](device-manager-images/win/20-created-device.png#lightbox)


### <a name="edit-device"></a>Editar dispositivo

Para editar un dispositivo virtual existente, selecciónelo y haga clic en el botón **Editar** (situado en la esquina superior derecha de la pantalla):

[![Botón Editar para modificar un dispositivo](device-manager-images/win/21-edit-button-sml.png)](device-manager-images/win/21-edit-button.png#lightbox)

Al hacer clic en **Editar**, se inicia el editor de dispositivos para el dispositivo virtual seleccionado:

[![Pantalla del editor de dispositivos](device-manager-images/win/22-device-editor-sml.png)](device-manager-images/win/22-device-editor.png#lightbox)

En la pantalla del **editor de dispositivos** se muestran las propiedades del dispositivo virtual en la columna **Propiedad**, con los valores correspondientes de cada propiedad en la columna **Valor**. Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha.

Para cambiar una propiedad, edite su valor en la columna **Valor**.
Por ejemplo, en la siguiente captura de pantalla, la propiedad `hw.lcd.density` se cambia de **480** a **240**:

[![Ejemplo de edición de dispositivo](device-manager-images/win/23-device-editing-sml.png)](device-manager-images/win/23-device-editing.png#lightbox)

Después de realizar los cambios de configuración necesarios, haga clic en el botón **Guardar**.
Para obtener más información sobre cómo cambiar las propiedades del dispositivo virtual, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).


### <a name="additional-options"></a>Opciones adicionales

En el menú desplegable **Opciones adicionales** (&hellip;) que está situado en la esquina superior derecha, hay opciones adicionales para trabajar con dispositivos:

[![Ubicación del menú de opciones adicionales](device-manager-images/win/24-overflow-menu-sml.png)](device-manager-images/win/24-overflow-menu.png#lightbox)

El menú de opciones adicionales contiene los elementos siguientes:

- **Duplicate and Edit** (Duplicar y editar): duplica el dispositivo seleccionado actualmente y lo abre en la pantalla **Nuevo dispositivo** con otro nombre exclusivo. Por ejemplo, al seleccionar **Pixel_API_27** y hacer clic en **Duplicar y editar**, se anexa un contador al nombre:

  [![Pantalla para duplicar y editar](device-manager-images/win/25-dupe-and-edit-sml.png)](device-manager-images/win/25-dupe-and-edit.png#lightbox)

- **Mostrar en el Explorador**: se abre una ventana del Explorador de Windows en la carpeta que contiene los archivos del dispositivo virtual. Por ejemplo, al seleccionar **Pixel_API_27** y hacer clic en **Mostrar en el Explorador**, se abre una ventana como en el ejemplo siguiente:

  [![Resultados de hacer clic en Mostrar en el Explorador](device-manager-images/win/26-reveal-in-explorer-sml.png)](device-manager-images/win/26-reveal-in-explorer.png#lightbox)

- **Restablecimiento de fábrica**: restablece el dispositivo seleccionado a sus valores predeterminados, para lo cual borra los cambios de usuario realizados en el estado interno del dispositivo mientras estaba en ejecución (esto también borra la captura de pantalla [Arranque rápido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) actual, si existe). Este cambio no altera las modificaciones que realice en el dispositivo virtual durante la creación y la edición. Aparecerá un cuadro de diálogo que le recordará que este restablecimiento no se puede deshacer. Haga clic en **Restablecimiento de fábrica** para confirmar el restablecimiento:

  ![Cuadro de diálogo de restablecimiento de fábrica](device-manager-images/win/27-factory-reset.png)

- **Eliminar**: elimina de forma permanente el dispositivo virtual seleccionado. Aparecerá un cuadro de diálogo que le recordará que la eliminación de un dispositivo no se puede deshacer. Haga clic en **Eliminar** si está seguro de que quiere eliminar el dispositivo.

  ![Cuadro de diálogo para eliminar el dispositivo](device-manager-images/win/28-delete-device-w158.png)


::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>Android Device Manager en macOS

En este artículo se explica cómo se usa Android Device Manager para crear, duplicar, personalizar e iniciar dispositivos virtuales Android.

[![Captura de pantalla de Android Device Manager en la pestaña Dispositivos](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> Esta guía solo se aplica a Visual Studio para Mac.
Xamarin Studio no es compatible con Android Device Manager.

Android Device Manager se usa para crear y configurar *dispositivos virtuales Android* (AVD) que se ejecutan en [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).
Cada AVD es una configuración del emulador que simula un dispositivo Android físico. Esto permite ejecutar y probar la aplicación en diversas configuraciones que simulan diferentes dispositivos Android físicos.

## <a name="requirements"></a>Requisitos

Para usar Android Device Manager, necesita los siguiente elementos:

- Visual Studio para Mac 7.6 o posterior.

- Debe estar instalado Android SDK (consulte [Configuración de Android SDK para Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

- Es necesario instalar los siguientes paquetes (a través de [Android SDK Manager](~/android/get-started/installation/android-sdk.md)): 
  - **SDK Tools versión 26.1.1** o posterior
  - **Herramientas de plataforma de Android SDK 28.0.1** o posterior 
  - **Herramientas de compilación de Android SDK 26.0.3** o posterior

  Estos paquetes deberían mostrarse con el estado **Instalado**, como se muestra en la captura de pantalla siguiente:

  [![Instalación de Android SDK Tools](device-manager-images/mac/02-sdk-tools-sml.png)](device-manager-images/mac/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>Iniciar el Administrador de dispositivos

Para iniciar Android Device Manager, haga clic en **Herramientas > Device Manager**:

[![Inicio del administrador de dispositivos desde el menú Herramientas](device-manager-images/mac/03-tools-menu-sml.png)](device-manager-images/mac/03-tools-menu.png#lightbox)

Si aparece el siguiente cuadro de diálogo de error al inicio, consulte la sección [Solución de problemas](#troubleshooting) para obtener soluciones alternativas:

![Cuadro de diálogo de error de instancia de Android SDK](device-manager-images/mac/04-sdk-instance-error.png)


## <a name="main-screen"></a>Pantalla principal

Cuando inicie el Administrador de dispositivos Android por primera vez, aparecerá una pantalla con todos los dispositivos virtuales configurados. Para cada dispositivo virtual, se muestran el **nombre**, el **SO** (versión de Android), el **procesador**, el tamaño de **memoria** y la **resolución** de pantalla:

[![Lista de los dispositivos instalados y sus parámetros](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

Al seleccionar un dispositivo de la lista, aparece el botón **Reproducir** a la derecha. Puede hacer clic en el botón **Reproducir** para iniciar el emulador con este dispositivo virtual:

[![Botón Reproducir de una imagen de dispositivo](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

Una vez que el emulador se haya iniciado con el dispositivo virtual seleccionado, el botón **Reproducir** cambiará al botón **Detener**, que puede usar para detener el emulador:

[![Botón Detener del dispositivo en ejecución](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

Al detener el emulador, puede que aparezca un aviso en el que se le pregunta si quiere guardar el estado actual para el siguiente arranque rápido:

![Cuadro de diálogo para guardar el estado actual para el arranque rápido](device-manager-images/mac/08-save-for-quick-boot-m76.png)

Si guarda el estado actual, el emulador se iniciará con mayor rapidez cuando este dispositivo virtual se vuelva a iniciar. Para obtener más información sobre el inicio rápido, consulte [Arranque rápido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot).

### <a name="new-device"></a>Dispositivo nuevo

Para crear un dispositivo, haga clic en el botón **Nuevo dispositivo** (situado en la parte superior izquierda de la pantalla):

[![Botón Nuevo para crear un dispositivo](device-manager-images/mac/09-new-button-sml.png)](device-manager-images/mac/09-new-button.png#lightbox)

Al hacer clic en **Nuevo dispositivo**, aparece la pantalla **Nuevo dispositivo**:

[![Pantalla Nuevo dispositivo del Administrador de dispositivos](device-manager-images/mac/10-new-device-editor-sml.png)](device-manager-images/mac/10-new-device-editor.png#lightbox)

Siga estos pasos para configurar un nuevo dispositivo en la pantalla **Nuevo dispositivo**:

1. Asigne un nuevo nombre al dispositivo. En el ejemplo siguiente, el nuevo dispositivo se denomina **Pixel_API_27**:

   [![Asignar un nombre al nuevo dispositivo](device-manager-images/mac/11-device-name-m76-sml.png)](device-manager-images/mac/11-device-name-m76.png#lightbox)

2. Seleccione el dispositivo físico que quiera emular; para ello, haga clic en el menú desplegable **Dispositivo base**:

   [![Seleccionar el dispositivo físico que se va a emular](device-manager-images/mac/12-device-menu-m76-sml.png)](device-manager-images/mac/12-device-menu-m76.png#lightbox)

3. Seleccione un tipo de procesador para este dispositivo virtual; para ello, haga clic en el menú desplegable **Procesador**. Si selecciona **x86**, se proporcionará el mejor rendimiento porque permite que el emulador aproveche las ventajas de la [aceleración de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md).
   La opción **x86_64** también usará la aceleración de hardware, pero la ejecución es un poco más lenta que **x86** (**x86_64** se suele usar para probar aplicaciones de 64 bits):

   [![Seleccionar el tipo de procesador](device-manager-images/mac/13-processor-type-menu-m76-sml.png)](device-manager-images/mac/13-processor-type-menu-m76.png#lightbox)

4. Seleccione la versión de Android (nivel de API); para ello, haga clic en el menú desplegable **SO**. Por ejemplo, seleccione **Oreo 8.1 - API 27** para crear un dispositivo virtual de nivel de API 27:

   [![Seleccionar la versión de Android](device-manager-images/mac/14-android-screenshot-m76-sml.png)](device-manager-images/mac/14-android-screenshot-m76.png#lightbox)

   Si selecciona un nivel de API de Android que aún no se ha instalado, Device Manager mostrará el mensaje **A new device will be downloaded** (Se descargará un nuevo dispositivo) en la parte inferior de la pantalla y descargará e instalará los archivos necesarios, además de crear el dispositivo virtual:

   ![Se descargará una nueva imagen de dispositivo](device-manager-images/mac/15-automatic-download-m76.png)

5. Si quiere incluir las API de Google Play Services en el dispositivo virtual, habilite la opción **Google APIs** (API de Google). Para incluir la aplicación de Google Play Store, habilite la opción **Google Play Store**:

   [![Seleccionar Google Play Services y Google Play Store](device-manager-images/mac/16-google-play-services-m76-sml.png)](device-manager-images/mac/16-google-play-services-m76.png#lightbox)

   Tenga en cuenta que las imágenes de Google Play Store solo están disponibles para algunos tipos de dispositivos base como Pixel, Pixel 2, Nexus 5 y Nexus 5X.

6. Edite las propiedades que necesite modificar. Para realizar cambios en las propiedades, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).

7. Agregue las propiedades adicionales que necesite establecer explícitamente. En la pantalla **Nuevo dispositivo** solo se muestran las propiedades que se modifican con más frecuencia, pero puede hacer clic en el menú desplegable **Agregar propiedad** (en la parte inferior) para agregar propiedades adicionales:

   [![Menú desplegable Agregar propiedad](device-manager-images/mac/17-add-property-menu-m76-sml.png)](device-manager-images/mac/17-add-property-menu-m76.png#lightbox)

   También puede definir una propiedad personalizada si hace clic en **Personalizado...** en la parte superior de esta lista de propiedades.

8. Haga clic en el botón **Crear** (esquina inferior derecha) para crear el dispositivo:

   ![Botón Crear](device-manager-images/mac/18-create-button-m76.png)

9. Android Device Manager agrega el nuevo dispositivo a la lista de dispositivos virtuales instalados, con el indicador de progreso **Creando** mientras crea el dispositivo:

   [![Indicador de progreso de creación](device-manager-images/mac/19-creating-the-device-m76-sml.png)](device-manager-images/mac/19-creating-the-device-m76.png#lightbox)

10. Una vez finalizado el proceso de creación, el nuevo dispositivo se muestra en la lista de dispositivos virtuales instalados con el botón **Iniciar**, listo para su inicio:

    [![Dispositivo recién creado listo para su inicio](device-manager-images/mac/20-created-device-m76-sml.png)](device-manager-images/mac/20-created-device-m76.png#lightbox)


### <a name="edit-device"></a>Editar dispositivo

Para editar un dispositivo virtual existente, seleccione el menú desplegable **Opciones adicionales** (icono de engranaje) y haga clic en **Editar**:

[![Selección del menú Editar para modificar un nuevo dispositivo](device-manager-images/mac/21-edit-button-m76-sml.png)](device-manager-images/mac/21-edit-button-m76.png#lightbox)

Al hacer clic en **Editar**, se inicia el editor de dispositivos para el dispositivo virtual seleccionado:

[![Pantalla del editor de dispositivos](device-manager-images/mac/22-device-editor-sml.png)](device-manager-images/mac/22-device-editor.png#lightbox)

En la pantalla del **editor de dispositivos** se muestran las propiedades del dispositivo virtual en la columna **Propiedad**, con los valores correspondientes de cada propiedad en la columna **Valor**. Cuando se selecciona una propiedad, aparece una descripción detallada de dicha propiedad a la derecha.

Para cambiar una propiedad, edite su valor en la columna **Valor**.
Por ejemplo, en la siguiente captura de pantalla, la propiedad `hw.lcd.density` se cambia de **480** a **240**:

[![Ejemplo de edición de dispositivo](device-manager-images/mac/23-device-editing-sml.png)](device-manager-images/mac/23-device-editing.png#lightbox)

Después de realizar los cambios de configuración necesarios, haga clic en el botón **Guardar**.
Para obtener más información sobre cómo cambiar las propiedades del dispositivo virtual, vea [Editing Android Virtual Device Properties](~/android/get-started/installation/android-emulator/device-properties.md) (Editar propiedades de dispositivos virtuales Android).


### <a name="additional-options"></a>Opciones adicionales

En el menú desplegable situado a la izquierda del botón **Reproducir** hay opciones adicionales para trabajar con dispositivos:

[![Ubicación del menú de opciones adicionales](device-manager-images/mac/24-overflow-menu-sml.png)](device-manager-images/mac/24-overflow-menu.png#lightbox)

El menú de opciones adicionales contiene los elementos siguientes:

- **Editar**: abre el dispositivo seleccionado actualmente en el editor de dispositivos, tal como se ha descrito anteriormente.

- **Duplicate and Edit** (Duplicar y editar): duplica el dispositivo seleccionado actualmente y lo abre en la pantalla **Nuevo dispositivo** con otro nombre exclusivo. Por ejemplo, al seleccionar **Pixel 2 API 28** y hacer clic en **Duplicar y editar**, se anexa un contador al nombre:

  [![Pantalla para duplicar y editar](device-manager-images/mac/25-dupe-and-edit-sml.png)](device-manager-images/mac/25-dupe-and-edit.png#lightbox)

- **Mostrar en Finder**: se abre una ventana del Finder de macOS en la carpeta que contiene los archivos del dispositivo virtual. Por ejemplo, al seleccionar **Pixel 2 API 28** y hacer clic en **Mostrar en Finder**, se abre una ventana como en el ejemplo siguiente:

  [![Resultados de hacer clic en Mostrar en Finder](device-manager-images/mac/26-reveal-in-finder-sml.png)](device-manager-images/mac/26-reveal-in-finder.png#lightbox)

- **Restablecimiento de fábrica**: restablece el dispositivo seleccionado a sus valores predeterminados, para lo cual borra los cambios de usuario realizados en el estado interno del dispositivo mientras estaba en ejecución (esto también borra la captura de pantalla [Arranque rápido](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot) actual, si existe). Este cambio no altera las modificaciones que realice en el dispositivo virtual durante la creación y la edición. Aparecerá un cuadro de diálogo que le recordará que este restablecimiento no se puede deshacer. Haga clic en **Restablecimiento de fábrica** para confirmar el restablecimiento.

  ![Cuadro de diálogo de restablecimiento de fábrica](device-manager-images/mac/27-factory-reset-m76.png)

- **Eliminar**: elimina de forma permanente el dispositivo virtual seleccionado. Aparecerá un cuadro de diálogo que le recordará que la eliminación de un dispositivo no se puede deshacer. Haga clic en **Eliminar** si está seguro de que quiere eliminar el dispositivo.

  ![Cuadro de diálogo para eliminar el dispositivo](device-manager-images/mac/28-delete-device-m76.png)

-----


<a name="troubleshooting" />

## <a name="troubleshooting"></a>Solución de problemas

En las secciones siguientes se explica cómo diagnosticar y solucionar los problemas que pueden producirse al usar Android Device Manager para configurar dispositivos virtuales.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK en ubicación no estándar

Normalmente, Android SDK se instala en la siguiente ubicación:

**C:\\Archivos de programa (x86)\\Android\\android-sdk**

Si el SDK no está instalado en esta ubicación, puede obtener este error al iniciar Android Device Manager:

![Error de instancia de Android SDK](device-manager-images/win/29-sdk-error.png)

Para evitar este problema, siga estos pasos:

1. En el escritorio de Windows, vaya a **C:\\Usuarios\\*nombre de usuario*\\AppData\\Roaming\\XamarinDeviceManager**:

   ![Ubicación del archivo de registro de Android Device Manager](device-manager-images/win/30-log-files.png)

2. Haga doble clic para abrir uno de los archivos de registro y busque la **ruta de acceso del archivo de configuración**. Por ejemplo:

   [![Ruta de acceso del archivo de configuración en el archivo de registro](device-manager-images/win/31-config-file-path-sml.png)](device-manager-images/win/31-config-file-path.png#lightbox)

3. Vaya a esta ubicación y haga doble clic en **user.config** para abrirlo.

4. En **user.config**, busque el elemento `<UserSettings>` y agréguele un atributo **AndroidSdkPath**. Establezca este atributo en la ruta de acceso en la que está instalado Android SDK en el equipo y guarde el archivo. Por ejemplo, `<UserSettings>` tendrá el aspecto siguiente si Android SDK se ha instalado en **C:\\Programas\\Android\\SDK**:

   ```xml
   <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:ProgramsAndroidSDK" />
   ```

Después de realizar este cambio en **user.config**, podrá iniciar Android Device Manager.


### <a name="wrong-version-of-android-sdk-tools"></a>Versión incorrecta de Android SDK Tools

Si no está instalado, podría ver este cuadro de diálogo de error en el inicio:

![Cuadro de diálogo de error de instancia de Android SDK](device-manager-images/win/32-sdk-instance-error.png)

Si ve este cuadro de diálogo de error, haga clic en **Abrir SDK Manager** para abrir Android SDK Manager. En Android SDK Manager, haga clic en la pestaña **Herramientas** e instale los siguientes paquetes:

- **Android SDK Tools 26.1.1** o posterior
- **Herramientas de plataforma de Android SDK 27.0.1** o posterior
- **Herramientas de compilación de Android SDK 27.0.3** o posterior


### <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

Si tiene un AVD configurado para Android Oreo con acceso Wi-Fi simulado, el reinicio del AVD después de una instantánea puede provocar la deshabilitación del acceso Wi-Fi.

Para evitar este problema:

1. Seleccione el AVD en Android Device Manager.

2. En el menú de opciones adicionales, haga clic en **Mostrar en el Explorador**.

3. Vaya a **snapshots > default_boot**.

4. Elimine el archivo **snapshot.pb**:

   ![Ubicación del archivo snapshot.pb](device-manager-images/win/33-delete-snapshot.png)

5. Reinicie el AVD.

Una vez realizados estos cambios, se reiniciará el AVD en un estado que permita que la conexión Wi-Fi vuelva a funcionar.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

### <a name="wrong-version-of-android-sdk-tools"></a>Versión incorrecta de Android SDK Tools

Si no está instalado, podría ver este cuadro de diálogo de error en el inicio:

![Cuadro de diálogo de error de instancia de Android SDK](device-manager-images/mac/29-sdk-instance-error.png)

Si ve este cuadro de diálogo de error, haga clic en **Aceptar** para abrir Android SDK Manager. En Android SDK Manager, haga clic en la pestaña **Herramientas** e instale los siguientes paquetes:

- **Android SDK Tools 26.1.1** o posterior
- **Herramientas de plataforma de Android SDK 28.0.1** o posterior
- **Herramientas de compilación de Android SDK 26.0.3** o posterior

### <a name="snapshot-disables-wifi-on-android-oreo"></a>La instantánea deshabilita la conexión WiFi en Android Oreo

Si tiene un AVD configurado para Android Oreo con acceso Wi-Fi simulado, el reinicio del AVD después de una instantánea puede provocar la deshabilitación del acceso Wi-Fi.

Para evitar este problema:

1. Seleccione el AVD en Android Device Manager.

2. En el menú de opciones adicionales, haga clic en **Mostrar en Finder**.

3. Vaya a **snapshots > default_boot**.

4. Elimine el archivo **snapshot.pb**:

   [![Ubicación del archivo snapshot.pb](device-manager-images/mac/30-delete-snapshot-sml.png)](device-manager-images/mac/30-delete-snapshot.png#lightbox)

5. Reinicie el AVD.

Una vez realizados estos cambios, se reiniciará el AVD en un estado que permita que la conexión Wi-Fi vuelva a funcionar.

-----

### <a name="generating-a-bug-report"></a>Generación de informes de errores

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Si encuentra algún problema con Android Device Manager que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error. Para ello, haga clic con el botón derecho en la barra del título y seleccione **Generate Bug Report** (Generar informe de errores):

[![Ubicación del elemento de menú para informar de un error](device-manager-images/win/34-bug-report-sml.png)](device-manager-images/win/34-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Si encuentra algún problema con Android Device Manager que no pueda resolver con los pasos anteriores para solucionar problemas, informe del error. Para ello, haga clic en **Ayuda > Notificar un problema**:

[![Ubicación del elemento de menú para informar de un error](device-manager-images/mac/31-bug-report-sml.png)](device-manager-images/mac/31-bug-report.png#lightbox)

::: zone-end

## <a name="summary"></a>Resumen

En esta guía se presenta la versión de Android Device Manager disponible en Visual Studio Tools para Xamarin y Visual Studio para Mac. Se explican características esenciales, como iniciar y detener el emulador de Android, seleccionar un dispositivo virtual Android (AVD) para su ejecución, crear dispositivos virtuales y editar un dispositivo virtual. Se explica cómo editar propiedades de hardware de generación de perfiles para aumentar la personalización, y se proporcionan sugerencias para solucionar problemas comunes.


## <a name="related-links"></a>Vínculos relacionados

- [Cambios en las Herramientas de Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
- [Notas de la versión de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/How-to-Create-and-Manage-Your-Own-Android-Emulators/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
