---
title: Emparejar con Mac para el desarrollo de Xamarin.iOS
description: En esta guía se describe cómo usar Emparejar con Mac para conectar Visual Studio 2019 a un host de compilación de Mac. Se describe cómo habilitar el inicio de sesión remoto en el equipo Mac, cómo conectarse al equipo Mac desde Visual Studio 2019, cómo agregar manualmente un host de compilación de Mac al equipo Windows y mucho más.
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: 7bc6a7ce1b8cd20aa782ddde42925e4e72374ec4
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996952"
---
# <a name="pair-to-mac-for-xamarinios-development"></a>Emparejar con Mac para el desarrollo de Xamarin.iOS

_En esta guía se describe cómo usar Emparejar con Mac para conectar Visual Studio 2019 a un host de compilación de Mac. Estas instrucciones también se aplican a Visual Studio 2017._

## <a name="overview"></a>Información general

Compilar aplicaciones nativas de iOS requiere acceso a las herramientas de compilación de Apple, que solo se ejecutan en equipos Mac. Por este motivo, Visual Studio 2019 debe conectarse a un equipo Mac accesible desde la red para compilar aplicaciones de Xamarin.iOS.

La característica Emparejar con Mac de Visual Studio 2019 detecta y recuerda los hosts de compilación de Mac, además de conectarse a ellos y autenticarse con ellos, a fin de que los desarrolladores de iOS basados en Windows puedan trabajar de forma productiva.

Emparejar con Mac permite el flujo de trabajo de desarrollo siguiente:

- Los desarrolladores pueden escribir código de Xamarin.iOS en Visual Studio 2019.

- Visual Studio 2019 abre una conexión de red a un host de compilación de Mac y usa las herramientas de compilación en esa máquina para compilar y firmar la aplicación de iOS.

- No es necesario ejecutar una aplicación independiente en el equipo Mac: Visual Studio 2019 invoca las compilaciones de Mac de forma segura a través de SSH.

- Se notifica a Visual Studio 2019 sobre los cambios en cuanto se producen. Por ejemplo, cuando un dispositivo iOS se conecta al equipo Mac o está disponible en la red, la barra de herramientas de iOS se actualiza al instante.

- Pueden conectarse simultáneamente varias instancias de Visual Studio 2019 al equipo Mac.

- Es posible usar la línea de comandos de Windows para compilar aplicaciones de iOS.

> [!NOTE]
>
> Antes de seguir las instrucciones de esta guía, complete los pasos siguientes:
>
> - En un equipo Windows, [instale Visual Studio 2019](~/get-started/installation/windows.md).
> - En un equipo Mac, [instale Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) y [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation).
>   - _Debe abrir manualmente Xcode después de realizar la instalación_ para poder agregar componentes adicionales.
>
> Si prefiere no instalar Visual Studio para Mac, Visual Studio 2019 puede configurar automáticamente el host de compilación de Mac con Xamarin.iOS y Mono.
> Todavía debe instalar y ejecutar Xcode.
> Para obtener más información, consulte [Aprovisionamiento automático de Mac](#automatic-mac-provisioning).

## <a name="enable-remote-login-on-the-mac"></a>Habilitar la sesión remota en el equipo Mac

Para configurar el host de compilación de Mac, habilite primero la sesión remota:

1. En el equipo Mac, abra el menú Preferencias del Sistema y vaya al panel **Compartir**.

2. Marque **Sesión remota** en la lista **Servicio**.

    ![Habilitación de Sesión remota](images/sharing.png "Habilitación de Sesión remota")

    Asegúrese de que está configurado para permitir el acceso de **Todos los usuarios** o que el nombre de usuario o el grupo de Mac se incluyen en la lista de usuarios permitidos.

3. Si se le pide, configure el firewall de macOS.

    Si ha establecido el firewall de macOS para bloquear las conexiones entrantes, puede que necesite permitir `mono-sgen` para recibir conexiones entrantes. Aparece un cuadro de diálogo de alerta para preguntarle si este es el caso.

4. Si se encuentra en la misma red que la máquina de Windows, Visual Studio 2019 ya debería detectar el equipo Mac. Si todavía no puede detectar el equipo Mac, pruebe a [agregar manualmente un equipo Mac](#manually-add-a-mac) o eche un vistazo a la [Guía de solución de problemas](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="connect-to-the-mac-from-visual-studio-2019"></a>Conectarse al equipo Mac desde Visual Studio 2019

Ahora que la sesión remota está habilitada, conecte Visual Studio 2019 al equipo Mac.

1. En Visual Studio 2019, abra un proyecto de iOS existente o elija **Archivo > Nuevo > Proyecto** y, después, seleccione una plantilla de proyecto de iOS para crear uno.

2. Abra el cuadro de diálogo **Emparejar con Mac**.

    - Use el botón **Emparejar con Mac** de la barra de herramientas de iOS:

      ![La barra de herramientas de iOS, con el botón Emparejar con Mac resaltado](images/ios-toolbar.png "La barra de herramientas de iOS, con el botón Emparejar con Mac resaltado")

    - O bien, seleccione **Herramientas > iOS > Emparejar con Mac**.

    - El cuadro de diálogo **Emparejar con Mac** muestra una lista de todos los hosts de compilación de Mac conectados anteriormente y disponibles actualmente:

      ![Cuadro de diálogo Emparejar con Mac](images/pairtomac.png "Cuadro de diálogo Emparejar con Mac")

3. Seleccione un equipo Mac en la lista. Haga clic en **Conectar**.

4. Escriba el nombre de usuario y la contraseña.

    - La primera vez que se conecta a cualquier equipo Mac, se le pide que escriba el nombre de usuario y la contraseña de esa máquina:

      ![Especificación de un nombre de usuario y una contraseña para el equipo Mac](images/auth.png "Especificación de un nombre de usuario y una contraseña para el equipo Mac")

      > [!TIP]
      > Al iniciar sesión, use el nombre de usuario del sistema en lugar del nombre completo.

    - Emparejar con Mac usa estas credenciales para crear una conexión SSH con el equipo Mac. Si se realiza correctamente, se agrega una clave al archivo **authorized_keys** en el equipo Mac. Las siguientes conexiones al mismo equipo Mac iniciarán sesión automáticamente.

5. Emparejar con Mac configura automáticamente el equipo Mac.

    [A partir de Visual Studio 2019 versión 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2019 instala o actualiza Mono y Xamarin.iOS en un host de compilación de Mac conectado según sea necesario (tenga en cuenta que Xcode se debe instalar manualmente). Consulte [Aprovisionamiento automático de Mac](#automatic-mac-provisioning) para obtener más información.

6. Busque el icono de estado de conexión.

    - Cuando Visual Studio 2019 está conectado a un equipo Mac, el elemento del equipo Mac del cuadro de diálogo **Emparejar con Mac** muestra un icono que indica que está conectado actualmente:

      ![Un equipo Mac conectado](images/connected.png "Un equipo Mac conectado")

      Solo puede haber un equipo Mac conectado en cualquier momento.

      > [!TIP]
      > Al hacer clic con el botón derecho en cualquier equipo Mac de la lista **Emparejar con Mac**, se abrirá un menú contextual que le permite **Conectar...** , **Olvidar este equipo Mac** o **Desconectar**:
      >
      > ![Menús contextuales de Emparejar con Mac](images/contextmenu.png "Menús contextuales de Emparejar con Mac")
      >
      > Si elige **Olvidar este equipo Mac**, se olvidarán sus credenciales del equipo Mac seleccionado. Para volver a conectarse a ese equipo Mac, debe volver a escribir el nombre de usuario y la contraseña.

Si se ha emparejado correctamente con un host de compilación de Mac, está listo para compilar aplicaciones de Xamarin.iOS en Visual Studio 2019. Eche un vistazo a la guía [Introducción a Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md).

Si no pudo emparejar un equipo Mac, pruebe a [agregar manualmente un equipo Mac](#manually-add-a-mac) o eche un vistazo a la [Guía de solución de problemas](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="manually-add-a-mac"></a>Agregar un equipo Mac manualmente

Si no ve un equipo Mac determinado en el cuadro de diálogo **Emparejar con Mac**, agréguelo manualmente:

1. Busque la dirección IP de su equipo Mac.

    - Abra **Preferencias del Sistema > Compartir > Sesión remota** en el equipo Mac:

      [![Dirección IP del equipo Mac en Preferencias del sistema> Compartir](images/sharing-ipaddress.png "Dirección IP del equipo Mac en Preferencias del sistema> Compartir")](images/sharing.png#lightbox)

    - Como alternativa, use la línea de comandos. En la terminal, use este comando:

      ```bash
      $ ipconfig getifaddr en0
      196.168.1.8
      ```

      En función de la configuración de red, puede que tenga que usar un nombre de interfaz que no sea `en0`. Por ejemplo: `en1`, `en2`, etc.

2. En el cuadro de diálogo **Emparejar con Mac** de Visual Studio 2019, seleccione **Agregar equipo Mac**:

    [![Botón Agregar Mac en el cuadro de diálogo Emparejar con Mac](images/addtomac.png "Botón Agregar Mac en el cuadro de diálogo Emparejar con Mac")](images/addtomac-large.png#lightbox)

3. Escriba la dirección IP del equipo Mac y haga clic en **Agregar**:

    ![Especificación de la dirección IP del equipo Mac](images/enteripaddress.png "Especificación de la dirección IP del equipo Mac")

4. Escriba el nombre de usuario y la contraseña del equipo Mac:

    ![Especificación de un nombre de usuario y una contraseña](images/auth.png "Especificación de un nombre de usuario y una contraseña")

   > [!TIP]
   > Al iniciar sesión, use el nombre de usuario del sistema en lugar del nombre completo.

5. Haga clic en **Iniciar sesión** para conectar Visual Studio 2019 al equipo Mac mediante SSH y agregarlo a la lista de equipos conocidos.

## <a name="automatic-mac-provisioning"></a>Aprovisionamiento automático del equipo Mac

A partir de la [versión 15.6 de Visual Studio 2019](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Emparejar con Mac aprovisiona automáticamente un Mac con el software necesario para compilar aplicaciones de Xamarin.iOS: Mono, Xamarin.iOS (el marco de software, no el IDE de Visual Studio para Mac) y varias herramientas relacionadas con Xcode (pero no el propio Xcode).

> [!IMPORTANT]
>
> - Emparejar con Mac no puede instalar Xcode; debe instalarlo manualmente en el host de compilación de Mac. Se requiere para el desarrollo de Xamarin.iOS.
> - El aprovisionamiento automático del equipo Mac requiere que esté habilitada la sesión remota en el equipo Mac y el equipo Windows debe poder acceder a este desde la red. Vea [Habilitar la sesión remota en el equipo Mac](#enable-remote-login-on-the-mac) para obtener más detalles.
> - El aprovisionamiento automático del equipo Mac requiere 3 GB de espacio libre en el equipo Mac para instalar Xamarin.iOS.

Emparejar con Mac realiza las instalaciones o actualizaciones de software necesarias cuando Visual Studio 2019 se [conecta al equipo Mac](#connect-to-the-mac-from-visual-studio-2019).

### <a name="mono"></a>Mono

Emparejar con Mac comprobará que esté instalado Mono. Si no está instalado, Emparejar con Mac descargará e instalará la versión estable más reciente de Mono en el equipo Mac.

El progreso se indica mediante varios mensajes, como se muestra en las siguientes capturas de pantalla (haga clic para ampliar):

|Mono|Comprobación de la instalación|Descarga|Instalación
|---|---|---|---|
||[![Falta la instalación de Mono](images/mono-missing.png "Falta la instalación de Mono")](images/mono-missing-large.png#lightbox)|[![Descarga de Mono](images/mono-downloading.png "Descarga de Mono")](images/mono-downloading-large.png#lightbox)|[![Instalación de Mono](images/mono-installing.png "Instalación de Mono")](images/mono-installing-large.png#lightbox)|

### <a name="xamarinios"></a>Xamarin.iOS

Emparejar con Mac actualiza Xamarin.iOS en el equipo Mac para que coincida con la versión instalada en el equipo Windows.

> [!IMPORTANT]
> Emparejar con Mac no cambiará Xamarin.iOS a una versión anterior en el equipo Mac desde alfa o beta a estable. Si tiene Visual Studio para Mac instalado, establezca su [canal de versión](https://docs.microsoft.com/visualstudio/mac/update) como se indica a continuación:
>
> - Si usa Visual Studio 2019, seleccione el canal de actualizaciones **Estable** en Visual Studio para Mac.
> - Si usa la versión preliminar de Visual Studio 2019, seleccione el canal de actualizaciones **Alfa** en Visual Studio para Mac.

El progreso se indica mediante varios mensajes, como se muestra en las siguientes capturas de pantalla (haga clic para ampliar):

|Xamarin.iOS|Comprobación de la instalación|Descarga|Instalación
|---|---|---|---|
||[![Falta la instalación de Xamarin.iOS](images/xamios-missing.png "Falta la instalación de Xamarin.iOS")](images/xamios-missing-large.png#lightbox)|[![Descarga de Xamarin.iOS](images/xamios-downloading.png "Descarga de Xamarin.iOS")](images/xamios-downloading-large.png#lightbox)|[![Instalación de Xamarin.iOS](images/xamios-installing.png "Instalación de Xamarin.iOS")](images/xamios-installing-large.png#lightbox)|

### <a name="xcode-tools-and-license"></a>Licencias y herramientas de Xcode

Emparejar con Mac también comprobará si se ha instalado Xcode y se ha aceptado su licencia. Aunque Emparejar con Mac no instala Xcode, solicita la aceptación de la licencia, como se muestra en las siguientes capturas de pantalla (haga clic para ampliar):

|Xcode|Comprobación de la instalación|Aceptación de la licencia|
|---|---|---|
||[![Falta la instalación de Xcode](images/xcode-missing.png "Falta la instalación de Xcode")](images/xcode-missing-large.png#lightbox)|[![Licencia de Xcode](images/xcode-license.png "Licencia de Xcode")](images/xcode-license-large.png#lightbox)|

Además, Emparejar con Mac instalará o actualizará varios paquetes distribuidos con Xcode. Por ejemplo:

- **MobileDeviceDevelopment.pkg**
- **XcodeExtensionSupport.pkg**
- **MobileDevice.pkg**
- **XcodeSystemResources.pkg**

La instalación de estos paquetes se produce rápidamente y sin preguntar al usuario.

> [!NOTE]
> Estas herramientas son distintas de las herramientas de la línea de comandos de Xcode, que se [instalan con Xcode](https://developer.apple.com/library/content/technotes/tn2339/_index.html) a partir de macOS 10.9.

### <a name="troubleshooting-automatic-mac-provisioning"></a>Solución de problemas de aprovisionamiento automático del equipo Mac

Si se produce algún problema con el aprovisionamiento automático del equipo Mac, eche un vistazo a los registros del IDE de Visual Studio 2019, que se almacenan en **%LOCALAPPDATA%\Xamarin\Logs\16.0**. Estos registros pueden contener mensajes de error que le ayudarán a diagnosticar mejor el error o recibir soporte técnico.

## <a name="build-ios-apps-from-the-windows-command-line"></a>Compilar aplicaciones de iOS desde la línea de comandos de Windows

Emparejar con Mac permite compilar aplicaciones de Xamarin.iOS desde la línea de comandos. Por ejemplo:

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

Los parámetros pasados a `msbuild` en el ejemplo anterior son:

- `ServerAddress`: dirección IP del host de compilación de Mac.
- `ServerUser`: nombre de usuario que se usará al iniciar sesión en el host de compilación de Mac.
  Use el nombre de usuario del sistema en lugar del nombre completo.
- `ServerPassword`: contraseña que se usará al iniciar sesión en el host de compilación de Mac.

> [!NOTE]
> Visual Studio 2019 almacena `msbuild` en el directorio siguiente: **C:\Program Files (x86)\Microsoft Visual Studio\2019\\&lt;Version&gt;\MSBuild\Current\Bin**

La primera vez que Emparejar con Mac inicia sesión en un host de compilación de Mac concreto desde Visual Studio 2019 o la línea de comandos, configura claves SSH. Con estas claves, no se necesitará el nombre de usuario ni la contraseña en los futuros inicios de sesión. Las claves recién creadas se almacenan en **%LOCALAPPDATA%\Xamarin\MonoTouch**.

Si se omite el parámetro `ServerPassword` de una invocación de compilación de la línea de comandos, Emparejar con Mac intenta iniciar sesión en el host de compilación de Mac mediante las claves SSH guardadas.

## <a name="summary"></a>Resumen

En este artículo se describe cómo usar Emparejar con Mac para conectar Visual Studio 2019 a un host de compilación de Mac, de forma que los desarrolladores de Visual Studio 2019 puedan compilar aplicaciones nativas de iOS con Xamarin.iOS.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de conexión](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Vídeo del agente de compilación de Mac de Xamarin](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
- [Introducción a Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Simulador de iOS remoto para Windows](~/tools/ios-simulator/index.md)
- [Implementación inalámbrica](~/ios/deploy-test/wireless-deployment.md)
