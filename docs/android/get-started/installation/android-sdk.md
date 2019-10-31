---
title: Configuración del SDK de Android para Xamarin.Android
description: Visual Studio incluye Android SDK Manager, que puede usar para descargar herramientas de Android SDK, plataformas y otros componentes necesarios para desarrollar aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 9d8b3e68aa626890fdce5c004928541ece263703
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020812"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>Configuración del SDK de Android para Xamarin.Android

_Visual Studio incluye Android SDK Manager, que puede usar para descargar herramientas de Android SDK, plataformas y otros componentes necesarios para desarrollar aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

En esta guía se explica cómo usar Android SDK Manager de Xamarin en Visual Studio y Visual Studio para Mac.

> [!NOTE]
> Esta guía se aplica a Visual Studio 2019, Visual Studio 2017 y Visual Studio para Mac.  

Android SDK Manager de Xamarin (instalado como parte de la carga de trabajo **Desarrollo para dispositivos móviles con .NET**) ayuda a descargar los componentes de Android más recientes que se necesitan para desarrollar la aplicación de Xamarin.Android. Reemplaza el SDK Manager independiente de Google, que está en desuso.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="requirements"></a>Requisitos

Para usar Android SDK Manager de Xamarin, necesita lo siguiente:

- Visual Studio 2019 Community, Professional o Enterprise.

- O bien, Visual Studio 2017 (edición Community, Professional o Enterprise). Se necesita Visual Studio 2017 versión 15.7 o posterior.

- Visual Studio Tools para Xamarin, versión 4.10.0 o posterior (instalada como parte de la carga de trabajo **Desarrollo para dispositivos móviles con .NET**). 

El Administrador de Android SDK de Xamarin también necesita disponer de Java Development Kit, que se instala automáticamente con Xamarin.Android. Hay varias alternativas de JDK para elegir:

- De forma predeterminada, Xamarin.Android usa [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), necesario para el desarrollo en el nivel de API 24 o superior. JDK 8 también admite niveles de API anteriores al 24.

- Si está desarrollando contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

- Si usa la versión preliminar 5 de Visual Studio 15.8 o una posterior, puede probar a usar la [distribución OpenJDK Mobile de Microsoft](openjdk.md) en lugar de JDK 8.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

## <a name="sdk-manager"></a>SDK Manager 

Para iniciar SDK Manager en Visual Studio, haga clic en **Herramientas > Android > Android SDK Manager**:

[![Ubicación del elemento de menú Android SDK Manager](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

Android SDK Manager se abre en la pantalla **Herramientas y Android SDK**. Esta pantalla tiene dos pestañas: **Plataformas** y **Herramientas**:

[![Captura de pantalla de Android SDK Manager abierto en la pestaña Plataformas](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

La pantalla **Herramientas y Android SDK** se describe con más detalle en las secciones siguientes.

### <a name="android-sdk-location"></a>Ubicación de Android SDK

La ubicación de Android SDK está configurada en la parte superior de la pantalla **Herramientas y Android SDK**, tal como se muestra en la imagen anterior. Esta ubicación se debe configurar correctamente para que las pestañas **Plataformas** y **Herramientas** funcionen correctamente. Es posible que deba establecer la ubicación de Android SDK por alguno de los motivos siguientes:

1. Android SDK Manager no pudo encontrar Android SDK. 

2. Ha instalado Android SDK en una ubicación alternativa (no predeterminada). 

Para establecer la ubicación de Android SDK, haga clic en el botón de puntos suspensivos (&hellip;) situado a la derecha de **Ubicación de Android SDK**. Se abrirá el cuadro de diálogo **Buscar carpeta**, que usará para navegar a la ubicación de Android SDK. En la siguiente captura de pantalla, está seleccionado Android SDK en **Archivos de programa (x86)\\Android**:

![Captura de pantalla del cuadro de diálogo de Windows Buscar carpeta para localizar Android SDK](android-sdk-images/win/05-browse-for-folder.png)

Al hacer clic en **Aceptar**, SDK Manager administrará el Android SDK instalado en la ubicación seleccionada.

### <a name="tools-tab"></a>Pestaña Herramientas

En la pestaña **Herramientas** se muestra una lista de _herramientas_ y _extras_. Use esta pestaña para instalar las herramientas de Android SDK, las herramientas de la plataforma y las herramientas de compilación.
Además, puede instalar Android Emulator, el depurador de bajo nivel (LLDB), el NDK, la aceleración de HAXM y bibliotecas de Google Play.

Por ejemplo, para descargar el paquete de Android Emulator de Google, haga clic en la marca de verificación situada junto a **Android Emulator** y, después, haga clic en el botón **Aplicar cambios**:

[![Instalación de Android Emulator desde la pestaña Herramientas](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

Puede aparecer un diálogo con el mensaje _El paquete siguiente requiere que acepte sus términos de licencia antes de instalarlo_:

![Pantalla de aceptación de licencia](android-sdk-images/win/07-license-acceptance.png)

Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Una vez finalizada la instalación, en la pestaña **Herramientas** se mostrará que se han instalado las herramientas y los extras seleccionados.

### <a name="platforms-tab"></a>Pestaña Plataformas

En la pestaña **Plataformas** se muestra una lista de versiones del SDK de la plataforma junto con otros recursos (por ejemplo, imágenes del sistema) para cada plataforma:

[![Captura de pantalla del panel Plataformas](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

En esta pantalla se muestra la versión de Android (como **Android 8.0**), el nombre en código (**Oreo**), el nivel de API (por ejemplo, **26**) y el tamaño de los componentes de esa plataforma (como **1 GB**). Use la pestaña **Plataformas** para instalar los componentes correspondientes al nivel de API de Android al que quiera dirigirse. Para obtener más información sobre las versiones de Android y los niveles de API, vea [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

Si están instalados todos los componentes de una plataforma, aparece una marca de verificación junto al nombre de la plataforma. Si no están instalados todos los componentes de una plataforma, se rellena el cuadro de dicha plataforma. Puede expandir una plataforma para ver sus componentes (y los componentes instalados). Para ello, haga en el cuadro **+** situado a la izquierda de la plataforma.
Haga clic en **-** para contraer la lista de componentes de una plataforma.

Para agregar otra plataforma al SDK, haga clic en el cuadro situado junto a la plataforma hasta que aparezca la marca de verificación para instalar todos sus componentes. Después, haga clic en **Aplicar cambios**:

[![Ejemplo de cómo agregar componentes de Android 7.1 Nougat a Android SDK](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

Para instalar solo componentes específicos, haga clic una vez en el cuadro situado junto a la plataforma. Después, puede seleccionar los componentes individuales que necesite:

[![Ejemplo de cómo agregar algunos componentes de Android 7.1](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

Observe que el número de componentes que se van a instalar aparece junto al botón **Aplicar cambios**. Tras hacer clic en el botón **Aplicar cambios**, verá la pantalla **Aceptación de licencia**, tal como se muestra anteriormente.
Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. Es posible que vea este cuadro de diálogo más de una vez si se van a instalar varios componentes. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Cuando se complete el proceso de descarga e instalación (puede tardar muchos minutos, en función de cuántos componentes deban descargarse), los componentes agregados se marcarán con una marca de verificación y a su lado se indicará **Instalado**.

### <a name="repository-selection"></a>Selección del repositorio

De forma predeterminada, Android SDK Manager descarga los componentes y las herramientas de la plataforma desde un repositorio administrado por Microsoft. Si necesita acceso a las plataformas y herramientas alfa/beta experimentales que todavía no están disponibles en el repositorio de Microsoft, puede cambiar SDK Manager para que use el repositorio de Google. Para realizar este cambio, haga clic en el icono de engranaje en la esquina inferior derecha y seleccione **Repositorio > Google (no compatible)** :

[![Selección del repositorio de Google](android-sdk-images/win/11-google-repo-w157-sml.png)](android-sdk-images/win/11-google-repo-w157.png#lightbox)

Al seleccionar el repositorio de Google, pueden aparecer paquetes adicionales en la pestaña **Plataformas** que no estaban disponibles anteriormente. (En la captura de pantalla anterior, **Android SDK Platform 28** se agregó al cambiar al repositorio de Google.) Tenga en cuenta que el uso del repositorio de Google no es dispone de soporte técnico y, por tanto, no se recomienda para el desarrollo habitual.

Para volver al repositorio compatible de plataformas y herramientas, haga clic en **Microsoft (recomendado)** . Esto restaura la lista de paquetes y las herramientas a la selección predeterminada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="requirements"></a>Requisitos

Para usar Android SDK Manager de Xamarin, necesita lo siguiente:

- Visual Studio para Mac 7.5 (o posterior).

El Administrador de Android SDK de Xamarin también necesita disponer de Java Development Kit, que se instala automáticamente con Xamarin.Android. Hay varias alternativas de JDK para elegir:

- De forma predeterminada, Xamarin.Android usa [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), necesario para el desarrollo en el nivel de API 24 o superior. JDK 8 también admite niveles de API anteriores al 24.

- Si está desarrollando contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

- Si usa Visual Studio para Mac 7.7 o versiones posteriores, puede probar a usar la [distribución OpenJDK Mobile de Microsoft](openjdk.md) en lugar de JDK 8.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

## <a name="sdk-manager"></a>SDK Manager 

Para iniciar SDK Manager en Visual Studio para Mac, haga clic en **Herramientas > SDK Manager**:

[![Ubicación del elemento de menú Android SDK Manager](android-sdk-images/mac/01-sdk-manager-menu-item-m75-sml.png)](android-sdk-images/mac/01-sdk-manager-menu-item-m75.png#lightbox)

**Android SDK Manager** se abre en la **ventana Preferencias**, que contiene tres pestañas, denominadas **Plataformas**, **Herramientas**y **Ubicaciones**:

[![Captura de pantalla de Android SDK Manager abierto en la pestaña Plataformas](android-sdk-images/mac/02-sdk-manager-platforms-m75-sml.png)](android-sdk-images/mac/02-sdk-manager-platforms-m75.png#lightbox)

Las pestañas de Android SDK Manager se describen en las secciones siguientes.

### <a name="locations-tab"></a>Pestaña Ubicaciones

La pestaña **Ubicaciones** tiene tres opciones para configurar las ubicaciones de Android SDK, Android NDK y el SDK de Java (JDK). Estas ubicaciones se deben configurar correctamente para que las pestañas **Plataformas** y **Herramientas** funcionen correctamente.

Cuando se inicia SDK Manager, determina automáticamente la ruta de acceso para cada paquete instalado e indica que se ha **Encontrado** y coloca un icono de marca de verificación verde junto a la ruta de acceso:

[![Captura de pantalla de la pestaña Ubicaciones](android-sdk-images/mac/03-locations-tab-m75-sml.png)](android-sdk-images/mac/03-locations-tab-m75.png#lightbox)

Haga clic en el botón **Restablecer valores predeterminados** para que SDK Manager busque el SDK, el NDK y el JDK en sus ubicaciones predeterminadas. 

Normalmente, usará la pestaña **Ubicaciones** para modificar la ubicación de Android SDK o del JDK de Java. No es necesario instalar el NDK para desarrollar aplicaciones de Xamarin.Android, ya que el NDK solo se usa cuando se necesita desarrollar partes de la aplicación mediante lenguajes de código nativo, como C y C++.

### <a name="tools-tab"></a>Pestaña Herramientas

En la pestaña **Herramientas** se muestra una lista de _herramientas_ y _extras_. Use esta pestaña para instalar las herramientas de Android SDK, las herramientas de la plataforma y las herramientas de compilación.
Además, puede instalar Android Emulator, el depurador de bajo nivel (LLDB), el NDK, la aceleración de HAXM y bibliotecas de Google Play.

Por ejemplo, para descargar el paquete de Android Emulator de Google, haga clic en la marca de verificación situada junto a **Android Emulator** y, después, haga clic en el botón **Aplicar cambios**:

[![Instalación de Android Emulator desde la pestaña Herramientas](android-sdk-images/mac/04-tools-tab-m75-sml.png)](android-sdk-images/mac/04-tools-tab-m75.png#lightbox)

Puede aparecer un diálogo con el mensaje _El paquete siguiente requiere que acepte sus términos de licencia antes de instalarlo_:

[![Pantalla de aceptación de licencia](android-sdk-images/mac/05-license-acceptance-m75-sml.png)](android-sdk-images/mac/05-license-acceptance-m75.png#lightbox)

Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Una vez finalizada la instalación, en la pestaña **Herramientas** se mostrará que se han instalado las herramientas y los extras seleccionados.

### <a name="platforms-tab"></a>Pestaña Plataformas

En la pestaña **Plataformas** se muestra una lista de versiones del SDK de la plataforma junto con otros recursos (por ejemplo, imágenes del sistema) para cada plataforma:

[![Captura de pantalla del panel Plataformas](android-sdk-images/mac/06-platforms-tab-m75-sml.png)](android-sdk-images/mac/06-platforms-tab-m75.png#lightbox)

En esta pantalla se muestra la versión de Android (como **Android 8.1**), el nombre de código (**Oreo**), el nivel de API (por ejemplo, **27**) y el tamaño de los componentes de esa plataforma (como **1 GB**). Use la pestaña **Plataformas** para instalar los componentes correspondientes al nivel de API de Android al que quiera dirigirse. Para obtener más información sobre las versiones de Android y los niveles de API, vea [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md).

Si están instalados todos los componentes de una plataforma, aparece una marca de verificación junto al nombre de la plataforma. Si no están instalados todos los componentes de una plataforma, se rellena el cuadro de dicha plataforma. Puede expandir una plataforma para ver sus componentes (y los componentes instalados). Para ello, haga en la **flecha** situada a la izquierda de la plataforma.
Haga clic en la **flecha abajo** para contraer la lista de componentes de una plataforma.

Para agregar otra plataforma al SDK, haga clic en el cuadro situado junto a la plataforma hasta que aparezca la marca de verificación para instalar todos sus componentes. Después, haga clic en **Aplicar cambios**:

[![Ejemplo de cómo agregar todos los componentes de una plataforma](android-sdk-images/mac/07-install-all-m75-sml.png)](android-sdk-images/mac/07-install-all-m75.png#lightbox)

Para instalar solo algunos componentes, haga clic una vez en el cuadro situado junto a la plataforma. Después, puede seleccionar los componentes individuales que necesite:

[![Ejemplo de cómo agregar algunos componentes](android-sdk-images/mac/08-individual-components-m75-sml.png)](android-sdk-images/mac/08-individual-components-m75.png#lightbox)

Observe que el número de componentes que se van a instalar aparece junto al botón **Aplicar cambios**. Tras hacer clic en el botón **Aplicar cambios**, verá la pantalla **Aceptación de licencia**, tal como se muestra anteriormente.
Haga clic en **Aceptar** si está de acuerdo con los términos y condiciones. Es posible que vea este cuadro de diálogo más de una vez si se van a instalar varios componentes. En la parte inferior de la ventana, una barra de progreso indica el progreso de la descarga y la instalación. Cuando se complete el proceso de descarga e instalación (puede tardar muchos minutos, en función de cuántos componentes deban descargarse), los componentes agregados se marcarán con una marca de verificación y a su lado se indicará **Instalado**.

### <a name="repository-selection"></a>Selección del repositorio

De forma predeterminada, Android SDK Manager descarga los componentes y las herramientas de la plataforma desde un repositorio administrado por Microsoft. Si necesita acceso a las plataformas y herramientas alfa/beta experimentales que todavía no están disponibles en el repositorio de Microsoft, puede cambiar SDK Manager para que use el repositorio de Google. Para realizar este cambio, haga clic en el icono de engranaje en la esquina inferior derecha y seleccione **Repositorio > Google (no compatible)** :

[![Selección del repositorio de Google](android-sdk-images/mac/09-google-repo-m75-sml.png)](android-sdk-images/mac/09-google-repo-m75.png#lightbox)

Al seleccionar el repositorio de Google, pueden aparecer paquetes adicionales en la pestaña **Plataformas** que no estaban disponibles anteriormente. (En la captura de pantalla anterior, **Android SDK Platform 28** se agregó al cambiar al repositorio de Google.) Tenga en cuenta que el uso del repositorio de Google no es dispone de soporte técnico y, por tanto, no se recomienda para el desarrollo habitual.

Para volver al repositorio compatible de plataformas y herramientas, haga clic en **Microsoft (recomendado)** . Esto restaura la lista de paquetes y las herramientas a la selección predeterminada.

-----

## <a name="summary"></a>Resumen

En esta guía se explica cómo instalar y usar la herramienta Android SDK Manager de Xamarin en Visual Studio y Visual Studio para Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Descripción de los niveles de API de Android](~/android/app-fundamentals/android-api-levels.md)
- [Cambios en las Herramientas de Android SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
