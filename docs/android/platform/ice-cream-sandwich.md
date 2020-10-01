---
title: Características de Ice Cream Sandwich
description: En este artículo se describen varias de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de Android 4 (Ice Cream Sandwich). Abarca varias nuevas tecnologías de interfaz de usuario y, a continuación, examina diversas nuevas funcionalidades que ofrece Android 4 para compartir datos entre aplicaciones y entre dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1e72a09acc08fc4db49da0e94eb64fbd523e9ecf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453694"
---
# <a name="ice-cream-sandwich-features"></a>Características de Ice Cream Sandwich

_En este artículo se describen varias de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de Android 4 (Ice Cream Sandwich). Abarca varias nuevas tecnologías de interfaz de usuario y, a continuación, examina diversas nuevas funcionalidades que ofrece Android 4 para compartir datos entre aplicaciones y entre dispositivos._

## <a name="overview"></a>Información general

La versión 4.0 del sistema operativo Android (nivel de API 14) representa un reprocesamiento importante del sistema operativo Android e incluye diversos cambios y actualizaciones pertinentes, entre los que se incluyen:

- **Interfaz de usuario actualizada**: varias nuevas características de la interfaz de usuario proporcionan a los desarrolladores mayor eficacia y flexibilidad cuando crean interfaces de usuario de aplicaciones. Entre estas nuevas características se incluyen: `GridLayout`, `PopupMenu`, el widget `Switch` y `TextureView`.
- **Mejor aceleración de hardware**: la representación 2D ahora tiene lugar en la GPU para todos los controles de Android. Además, la aceleración de hardware está activada, de forma predeterminada, en todas las aplicaciones desarrolladas para Android 4.0.
- **Nuevas API de datos**: existe un nuevo acceso a los datos a los que anteriormente no se podía tener acceso de forma oficial, como los datos de calendario y el perfil de usuario del propietario del dispositivo.
- **Uso compartido de datos de la aplicación**: compartir datos entre aplicaciones y dispositivos ahora es más fácil que nunca a través de tecnologías como `ShareActionProvider`, que facilita la creación de una acción de uso compartido a partir de una barra de acciones, y *Android Beam* para *Transmisión de datos en proximidad (NFC)* , que facilita el uso compartido de datos entre dispositivos muy próximos unos con otros.

En este artículo, vamos a explorar estas características y otros cambios que se han realizado en la API de Android 4.0 y explicaremos cómo usar cada característica con Xamarin.Android.

## <a name="user-interface-features"></a>Características de la interfaz de usuario

Hay diversas nuevas tecnologías de interfaz de usuario disponibles con Android 4, entre las que se incluyen:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** : admite el diseño de cuadrícula 2D de los controles.
- **[Widget Switch](~/android/user-interface/controls/switch.md)** : permite alternar entre ACTIVADO o DESACTIVADO.
- **[TextureView](~/android/user-interface/controls/texture-view.md)** : habilita el contenido de OpenGL y de vídeo en una vista.
- **[Barra de navegación](~/android/user-interface/controls/navigation-bar.md)** : contiene botones virtuales para atrás, inicio y multitarea.

Además, se han mejorado otros elementos de interfaz de usuario como, por ejemplo, `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, con el que ahora es más fácil trabajar, y pestañas, que presentan un aspecto más pulido.

## <a name="sharing-features"></a>Características de uso compartido

Android 4 incluye varias nuevas tecnologías que nos permiten compartir datos entre dispositivos y entre aplicaciones. También proporciona acceso a distintos tipos de datos que no estaban disponibles anteriormente, como información de calendario y el perfil de usuario del propietario del dispositivo. En esta sección, examinaremos diversas características ofrecidas por Android 4 que abordan estas áreas, entre las que se incluyen:

- **[Android Beam](~/android/platform/android-beam.md)** : permite el uso compartido de datos a través de NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** : crea un proveedor que permite a los desarrolladores especificar acciones de uso compartido desde la barra de acciones.
- **[Perfil de usuario](~/android/user-interface/user-profile.md)** : proporciona acceso a datos de perfil del propietario del dispositivo.
- **[API de calendario](~/android/user-interface/controls/calendar.md)** : proporciona acceso a datos de calendario del proveedor de calendarios.

## <a name="x86-emulators"></a>Emuladores x86

ICS aún no admite el desarrollo con un emulador x86. Los emuladores x86 solo se admiten con Android 2.3.3, nivel de API 10. Consulte [Configurar el emulador x86](~/android/get-started/installation/android-emulator/index.md) para obtener más información.

## <a name="summary"></a>Resumen

En este artículo se han tratado varias de las nuevas tecnologías que actualmente se encuentran disponibles con Android 4. Hemos revisado nuevas características de la interfaz de usuario como, por ejemplo, *GridLayout*, *PopupMenu* y el widget *Switch*. También hemos examinado parte de la nueva disponibilidad para controlar la interfaz de usuario del sistema, además de cómo trabajar con *TextureView*. A continuación, hemos explicado diversas nuevas tecnologías de uso compartido. Hemos hablado de cómo *Android Beam* le permite compartir información entre dispositivos que usan *NFC*, hemos explicado la nueva *API de calendario* y también hemos mostrado cómo usar el *ShareActionProvider* integrado.
Por último, hemos examinado cómo usar el proveedor *ContactsContract* para tener acceso a datos del perfil de usuario.

## <a name="related-links"></a>Vínculos relacionados

- [TextureViewDemo (ejemplo)](/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (ejemplo)](/samples/xamarin/monodroid-samples/calendardemo)
- [Tutorial de diseño de pestañas](~/android/user-interface/layouts/tab-layout/index.md)
- [Ice Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma Android 4.0](https://developer.android.com/about/versions/android-4.0.html)