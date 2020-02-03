---
title: Características del bocadillo de helado
description: En este artículo se describen algunas de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de Android 4, el bocadillo de helado. Abarca varias nuevas tecnologías de interfaz de usuario y, a continuación, examina una variedad de nuevas funcionalidades que Android 4 ofrece para compartir datos entre aplicaciones y entre dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4fbbe1bec317e66166d5218ef0ed54247aa4f6dd
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724380"
---
# <a name="ice-cream-sandwich-features"></a>Características del bocadillo de helado

_En este artículo se describen algunas de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de Android 4, el bocadillo de helado. Abarca varias nuevas tecnologías de interfaz de usuario y, a continuación, examina una variedad de nuevas funcionalidades que Android 4 ofrece para compartir datos entre aplicaciones y entre dispositivos._

## <a name="overview"></a>Información general

La versión 4,0 del SO Android (nivel de API 14) representa un retrabajo importante del sistema operativo Android e incluye una serie de cambios importantes y actualizaciones, entre los que se incluyen:

- **Interfaz de usuario actualizada** : varias características nuevas de la interfaz de usuario proporcionan a los desarrolladores más capacidad y flexibilidad cuando crean interfaces de usuario de la aplicación. Estas nuevas características incluyen: `GridLayout`, `PopupMenu`, `Switch` widget y `TextureView`.
- **Mejor aceleración de hardware** : ahora, la representación en 2D tiene lugar en la GPU para todos los controles de Android. Además, la aceleración de hardware está activada de forma predeterminada en todas las aplicaciones desarrolladas para Android 4,0.
- **Nuevas API de datos** : hay un nuevo acceso a los datos a los que no se podía tener acceso oficialmente, como los datos de calendario y el perfil de usuario del propietario del dispositivo.
- **Uso compartido de datos** de aplicaciones: el uso compartido de datos entre aplicaciones y dispositivos es ahora más fácil que nunca a través de tecnologías como el `ShareActionProvider`, lo que facilita la creación de una acción de uso compartido desde un barra de acciones y un *haz de Android* para la transmisión de datos en *proximidad (NFC)* , lo que lo convierte en un ajuste para compartir datos entre dispositivos en estrecha proximidad entre sí.

En este artículo, vamos a explorar estas características y otros cambios que se han realizado en la API de Android 4,0 y explicaremos cómo usar cada característica con Xamarin. Android.

## <a name="user-interface-features"></a>Características de la interfaz de usuario

Hay una variedad de nuevas tecnologías de interfaz de usuario disponibles con Android 4, entre las que se incluyen:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** : admite el diseño de cuadrícula 2D de los controles.
- **[Widget de conmutador](~/android/user-interface/controls/switch.md)** : permite alternar entre activado o desactivado.
- **[TextureView](~/android/user-interface/controls/texture-view.md)** : habilita el contenido de vídeo y OpenGL en una vista.
- **[Barra de navegación](~/android/user-interface/controls/navigation-bar.md)** : contiene botones virtuales para la parte posterior, la Página principal y la multitarea.

Además, se han mejorado otros elementos de la interfaz de usuario, como el `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, que ahora es más fácil de trabajar y las pestañas, que tienen un aspecto más pulido.

## <a name="sharing-features"></a>Compartir características

Android 4 incluye varias tecnologías nuevas que nos permiten compartir datos entre dispositivos y aplicaciones. También proporciona acceso a varios tipos de datos que no estaban disponibles anteriormente, como información de calendario y el perfil de usuario del propietario del dispositivo. En esta sección, examinaremos una variedad de características que ofrece Android 4 que abordan estas áreas, entre las que se incluyen:

- **[Carretera de Android](~/android/platform/android-beam.md)** : permite el uso compartido de datos a través de NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** : crea un proveedor que permite a los desarrolladores especificar acciones de uso compartido desde el barra de acciones.
- **[Perfil de usuario](~/android/user-interface/user-profile.md)** : proporciona acceso a los datos de perfil del propietario del dispositivo.
- **[Calendar API](~/android/user-interface/controls/calendar.md)** : proporciona acceso a los datos de calendario desde el proveedor de calendario.

## <a name="x86-emulators"></a>Emuladores x86

ICS todavía no admite el desarrollo con un emulador de x86. los emuladores x86 solo se admiten con Android 2.3.3, nivel de API 10. Para obtener más información, consulte [configuración del emulador de x86](~/android/get-started/installation/android-emulator/index.md) .

## <a name="summary"></a>Resumen

En este artículo se han tratado varias tecnologías nuevas que ahora están disponibles con Android 4. Hemos revisado nuevas características de la interfaz de usuario, como *GridLayout*, *popupMenu*y *Switch* widget. También analizamos parte de la nueva compatibilidad para controlar la interfaz de usuario del sistema, así como cómo trabajar con *TextureView*. A continuación, analizamos una serie de nuevas tecnologías de uso compartido. Hemos explicado cómo el *haz de Android* permite compartir información entre dispositivos que usan *NFC*, se ha EXPLICAdo la nueva *API de calendario*y también se ha mostrado cómo usar la *ShareActionProvider*integrada.
Por último, hemos examinado cómo usar el proveedor *ContactsContract* para acceder a los datos de Perfil de usuario.

## <a name="related-links"></a>Vínculos relacionados

- [TextureViewDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Tutorial de diseño de pestañas](~/android/user-interface/layouts/tab-layout/index.md)
- [Bocadillo de helado](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma Android 4,0](https://developer.android.com/about/versions/android-4.0.html)
