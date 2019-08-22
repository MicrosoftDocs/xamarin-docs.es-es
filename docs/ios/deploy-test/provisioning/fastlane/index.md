---
title: Introducción a fastlane para iOS
description: En esta guía se presentan las diversas herramientas de Fastlane que se pueden usar para firmar el código de aplicaciones de iOS. Se describe cómo actualizar, instalar y usar las herramientas de fastlane.
ms.prod: xamarin
ms.assetid: 8202C57D-22FF-4224-A5B1-AAEF12B7C106
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: becf9696a13dbd1ae96693729618d70b043d1c3a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527422"
---
# <a name="introduction-to-fastlane-for-ios"></a>Introducción a fastlane para iOS

fastlane es un proyecto de código abierto creado para simplificar el proceso confuso y a menudo tedioso de lanzar aplicaciones iOS y Android. Consta de varias utilidades, cada una de las cuales se encarga de un aspecto específico del lanzamiento de la aplicación, por ejemplo:

- [deliver](https://github.com/fastlane/fastlane/tree/master/deliver#readme): administra y carga capturas de pantalla, metadatos y lotes de aplicaciones en iTunes Connect.
- [produce](https://github.com/fastlane/fastlane/tree/master/produce#readme): crea una aplicación en iTunes Connect y en el portal para desarrolladores (que a menudo se conoce como AppID). También incluye compatibilidad con grupos de aplicaciones y servicios de aplicación.
- [pem](https://github.com/fastlane/fastlane/tree/master/pem#readme): crea y administra perfiles de aprovisionamiento de notificaciones de inserción.
- [gym](https://github.com/fastlane/fastlane/tree/master/gym#readme): se puede usar para compilar y firmar una aplicación de iOS. (Las aplicaciones Xamarin ya usan MSBuild para compilar, firmar y archivar aplicaciones).
- [cert](https://github.com/fastlane/fastlane/tree/master/cert#readme): crea y administra certificados de firma de código. 
- [sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme): crea y administra perfiles de aprovisionamiento.
- [match](https://github.com/fastlane/fastlane/tree/master/match#readme): crea y mantiene los certificados y los perfiles y los almacena en un repositorio Git para que se pueden sincronizar en un equipo de desarrollo.

fastlane se puede usar de maneras diferentes: a través de comandos de terminal, a través de medios basados en archivos o a través de variables de entorno para compilaciones de integración continua. 

Esta guía trata específicamente sobre la configuración de un dispositivo para el desarrollo con aplicaciones de iOS y se centra en las utilidades **cert**, **sigh** y **match**. 

El contenido proporcionado se puede usar como punto de partida para la distribución de aplicaciones, incluida la automatización completa del proceso en un servidor de integración continua. Sin embargo, es importante tener en cuenta que fastlane es un tercero que desarrolla herramientas para proyectos Xcode, de modo que es posible que algunas herramientas o comandos, como `fastlane init`, no funcionen correctamente con archivos csproj. Para más información sobre el uso de fastlane, las herramientas adicionales o el lanzamiento para Android mediante fastlane, consulte [https://fastlane.tools/](https://fastlane.tools/).

<a name="Installation" />

## <a name="installation"></a>Instalación

1. Asegúrese de que las herramientas de línea de comandos de Xcode están instaladas en la máquina macOS. Para instalar las herramientas, use el comando `xcode-select --install` en Terminal. Si ya están instaladas, se mostrará el siguiente error:

    ```bash
    error: command line tools are already installed, use "Software Update" to install updates
    ```

2. Descargue las herramientas de fastlane de [https://download.fastlane.tools](https://download.fastlane.tools). 

    > [!NOTE]
    > Es posible instalar las herramientas de fastlane desde Homebrew mediante `brew cask install fastlane` o a través de Rubygems (2.0 o versiones superiores) mediante `sudo gem install fastlane –NV`. De todos modos, el instalador garantizará que las dependencias correctas estén disponibles. 

3. Para instalar Fastlane, descomprima el archivo y haga doble clic en el ejecutable `install`. Si recibe un error que le advierte de que el archivo “no se puede abrir porque pertenece a un desarrollador sin identificar”, pulse Aceptar y haga lo siguiente:
    - Mientras mantiene presionado Ctrl, haga clic en el ejecutable `install`. Esto hará que se muestre este cuadro de diálogo:

     ![](images/fastlane-image12.png "Cuadro de diálogo de instalación")

    - Haga clic en Aceptar para iniciar la instalación de las herramientas de Fastlane.

4. Aparecerá el diálogo que se muestra a continuación en la terminal. Presione `y`:

   ![](images/fastlane-image13.png "Símbolo del sistema de Terminal")

5. Ejecute `which fastlane` antes de usar Fastlane por primera vez. La ruta de acceso debe tener este aspecto: 

    ```bash
    /Users/[user]/.fastlane/bin
    ```

6. Si la ruta de acceso coincide con la anterior, ya puede empezar.

     De lo contrario, haga lo siguiente:  En macOS, abra `.bash_profile`, que es un archivo oculto de texto no cifrado del directorio principal, con el siguiente comando:

    ```bash
    open ~/.bash_profile
    ```

7. Agregue la siguiente variable de entornoPATH y guárdelo: 

    ```bash
    export PATH="$HOME/.fastlane/bin:$PATH"
    ```

8. Ejecute `which fastlane` de nuevo para confirmar la ruta de acceso es similar a `/Users/[user]/.fastlane/bin`.


## <a name="updating-fastlane"></a>Actualización de fastlane

fastlane es un proyecto de código abierto muy activo que inserta a menudo versiones nuevas. Cuando haya disponible una versión nueva de fastlane, se le aconsejará lo siguiente al ejecutar un comando de fastlane:

[![](images/fastlane-image0.png "Símbolo del sistema de actualización de Fastlane")](images/fastlane-image0.png#lightbox)


Para actualizar a una versión nueva de Fastlane, descargue el paquete más reciente desde [aquí](https://download.fastlane.tools) y haga doble clic en el paquete de instalación para ejecutarlo:

[![](images/fastlane-image0a.png "Ejecución del paquete de instalación")](images/fastlane-image0a.png#lightbox)


## <a name="contents"></a>Contenido

En esta serie de guías se presentan algunas de las herramientas que Fastlane usa para firmar el código de la aplicación de iOS como preparación para el desarrollo o la distribución. Las herramientas que se tratan actualmente son las siguientes:

- [Cert](~/ios/deploy-test/provisioning/fastlane/cert.md)
- [Sigh](~/ios/deploy-test/provisioning/fastlane/sigh.md)
- [Match](~/ios/deploy-test/provisioning/fastlane/match.md)

cert y sigh se encargan de crear y administrar certificados de firma y perfiles de aprovisionamiento en una máquina local. match lleva este proceso un poco más allá, puesto que crea y administra los certificados y los perfiles de aprovisionamiento y los almacena en un repositorio Git, de modo que están siempre accesibles para todos los miembros de un equipo de desarrollo. Lea cada sección para descubrir cómo funcionan y cómo se usan.

## <a name="using-fastlane-tools-with-xamarin"></a>Uso de las herramientas de fastlane con Xamarin

Cuando haya creado una identidad de firma y los archivos de aprovisionamiento con fastlane, el proceso para configurar las opciones del lote de firma en Visual Studio para Mac debería ser rápido y sencillo, siempre que los certificados y las claves privadas se encuentren en la cadena de claves de macOS y los perfiles de aprovisionamiento en la carpeta `~/Library/MobileDevice/Provisioning Profiles`.

Para establecer las opciones de firma de código para una aplicación de Xamarin.iOS, haga clic con el botón derecho en el nombre del proyecto, seleccione **Opciones del proyecto > Compilar > Firma del lote de iOS** y establezca la identidad de firma y el perfil de aprovisionamiento explícitamente, como se muestra a continuación:

[![](images/fastlane-image11.png "Establecimiento explícito de la identidad de firma y el perfil de aprovisionamiento")](images/fastlane-image11.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Documentos de fastlane](https://fastlane.tools/)
- [Documentos de firma de código de fastlane](https://docs.fastlane.tools/codesigning/getting-started/)
- [Guía de firma de código](https://codesigning.guide/)
