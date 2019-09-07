---
title: Introducción a tvOS 9
description: En este artículo se presentan todas las API y características nuevas y modificadas disponibles en tvOS 9 para desarrolladores de Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/07/2016
ms.openlocfilehash: ecf5a7cabb03cea92075127d4d5e87350f45619e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769119"
---
# <a name="introduction-to-tvos-9"></a>Introducción a tvOS 9

_En este artículo se presentan todas las API y características nuevas y modificadas disponibles en tvOS 9 para desarrolladores de Xamarin. tvOS._

Apple ha lanzado la cuarta generación del hardware de Apple TV, que incluye un remoto rediseñado y habilitado para Touch, que ejecuta el nuevo sistema operativo tvOS (basado en iOS 9).

Por primera vez, tvOS abre la plataforma Apple TV para el desarrollador, lo que le permite crear sofisticadas aplicaciones envolventes y publicarlas a través de la tienda de aplicaciones integrada de Apple TV en un proceso similar a la experiencia de escritura y publicación de aplicaciones para iOS mediante iTunes App. Restaura.

Si está familiarizado con el desarrollo de Xamarin. iOS, debe encontrar la transición a tvOS bastante sencilla. La mayoría de las API y características son las mismas; sin embargo, muchas API comunes no están disponibles (como WebKit). Además, al trabajar con el Siri remoto se plantean algunos desafíos de diseño que no están presentes en los dispositivos iOS basados en la pantalla táctil.

En esta guía se ofrece una introducción a todas las API y características nuevas y modificadas disponibles en tvOS 9 para desarrolladores de Xamarin. tvOS. Para obtener más información sobre tvOS, consulte desarrollo de Apple [para la nueva documentación de Apple TV](https://developer.apple.com/tvos/) .

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Capacidades admitidas y no admitidas

las aplicaciones tvOS que se ejecutan en Apple TV tienen las siguientes funcionalidades y características admitidas:

- Grupos de aplicaciones
- Modos en segundo plano
- Protección de datos
- Game Center
- Dispositivos de juego
- iCloud
- Compras desde la aplicación
- Uso compartido de cadenas de claves

No se admiten las siguientes características y capacidades:

- Apple Pay
- Espacio aislado de la aplicación
- Dominios asociados
- HealthKit
- HomeKit
- Inter-App Audio
- Asignaciones
- VPN personal
- Notificaciones push
- Cartera
- Configuración inalámbrica de accesorios

Para obtener más información, consulte la documentación de los [ensamblados admitidos](~/ios/tvos/internals/assemblies.md) y los [marcos de trabajo admitidos](~/ios/tvos/internals/frameworks.md).

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Hardware de Apple TV

El nuevo Apple TV tiene las siguientes especificaciones de hardware:

- procesador A8 de 64 bits
- 32 GB o 64 GB de almacenamiento
- 2 GB de RAM
- Ethernet de 10/100 Mbps
- Wi-Fi 802.11 a/b/g/n/AC
- resolución de 1080p
- HDMI
- Puerto USB C (solo para uso del desarrollador y diagnóstico)
- Nuevo Siri remoto o Apple TV remoto (basado en la región)

### <a name="siri-remote"></a>Siri remoto

En función de la región, el Remote TV remoto proporcionado tendrá una configuración: Siri remoto o Apple TV remoto.

Siri Remote está disponible actualmente en los siguientes países:

- Australia
- Canadá
- Francia
- Alemania
- Japón
- España
- Reino Unido
- Estados Unidos

Todos los demás países recibirán el control remoto de Apple TV, que reemplaza el botón de Siri por un botón de búsqueda que muestra la pantalla de búsqueda predeterminada con entrada de texto para realizar búsquedas:

[![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png#lightbox)

Para obtener más información, consulte la documentación de [Siri Remote and Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Aprovisionamiento de Apple TV

Al igual que el desarrollo para iOS, el nuevo tvOS requerirá el perfil de aprovisionamiento adecuado tanto para el desarrollo como para la distribución en función de las identidades de suscripción y firma del equipo que ya haya establecido con Apple.

También es necesario el aprovisionamiento adecuado para obtener acceso a las características de tvOS, como los almacenes de datos de iCloud KVS o CloudKit. Consulte nuestros [recursos y el almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obtener información sobre la compatibilidad de icloud en las aplicaciones de Xamarin. tvOS.

Los perfiles de aprovisionamiento se crean e instalan de la misma manera que cuando se trabaja con aplicaciones de Xamarin. iOS. Como tal, consulte nuestra documentación de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) iOS para obtener más detalles.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Aplicaciones de Apple TV

El nuevo hardware de Apple TV y tvOS 9 admite dos tipos de aplicaciones: las aplicaciones tradicionales y de cliente-servidor.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Aplicaciones tradicionales

Las aplicaciones tradicionales se compran desde Apple TV App Store y se instalan directamente en el dispositivo. Estas aplicaciones pueden ser juegos, utilidades o aplicaciones multimedia desarrolladas con los mismos Marcos y técnicas que las aplicaciones de Xamarin. iOS.

Las aplicaciones de Apple TV tienen un tamaño máximo de 200 MB y pueden descargar 2 GB de contenido adicionales mediante recursos a petición. Para obtener más información, consulte [los recursos y el almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) .

Vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md) para familiarizarse con las herramientas y los conceptos necesarios para desarrollar aplicaciones de TvOS con Xamarin. tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Aplicaciones cliente-servidor

Además de las aplicaciones tradicionales instaladas, Apple TV facilita la creación de aplicaciones de streaming de multimedia cliente-servidor basadas en Web mediante tecnologías web (HTTPS, XML y JavaScript). Diseñará la interfaz de usuario mediante el lenguaje de marcado TVML de Apple y usará JavaScript para definir los comportamientos de la aplicación mediante TVMLKit.

Para obtener más información, consulte referencia del [lenguaje de marcado de Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)de Apple, referencia de [TVJS Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [referencia de TVMLKit Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [acerca de http Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) y la [especificación de creación de HLS para Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) . documentación.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Desafíos de la interfaz de usuario

A diferencia de iOS u OS X, Apple TV no tiene una pantalla táctil o un mouse que permita al usuario seleccionar directamente e interactuar con una aplicación o su contenido. En su lugar, el usuario es el nuevo Siri remoto o un dispositivo de juego Bluetooth para navegar por la interfaz de usuario de una aplicación. Para obtener más información, consulte la documentación de [Siri Remote and Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

Además, la experiencia global del usuario es drásticamente diferente de las aplicaciones de iOS o Mac que tienden a ser experiencias de usuario único. Con Apple TV, las experiencias de usuario tienden a ser más sociales por naturaleza, donde varias personas pueden estar sentado en el sofá interactuando con una sola aplicación y entre sí. Para diseñar una experiencia de aplicación de Apple TV correcta (ya sea una nueva aplicación o migrar una existente), deben tenerse en cuenta estos cambios. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Trabajar con imágenes de foco y Parallax

Como se indicó anteriormente, los usuarios de la aplicación de Xamarin. tvOS no interactuarán con su interfaz directamente como con iOS, donde tocan imágenes en la pantalla del dispositivo, pero indirectamente desde el salón mediante el uso remoto de Siri. Para presentar y controlar esta interacción del usuario, Apple TV usa un modelo basado en el enfoque. 

A medida que cambian los focos, se usan animaciones y efectos sutiles (como el efecto de Parallax en las imágenes) para identificar claramente el elemento de la interfaz de usuario que tiene el foco actualmente.

Si el usuario realiza un gesto circular lento en el Siri remoto, el elemento con el foco se verá en tiempo real en respuesta a este movimiento. A medida que se produce el Sway, se aplica un brillo iluminado a su imagen, lo que hará que la superficie parezca brillante. Después de una cantidad determinada de inactividad, el contenido desenfocado se atenúa y el elemento enfocado crecerá aún más.

Para obtener más información, consulte la documentación sobre [Cómo trabajar con navegación y centrar](~/ios/tvos/app-fundamentals/navigation-focus.md) y [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>La pantalla principal

La pantalla principal de Apple TV muestra todas las aplicaciones que están instaladas y proporciona una forma de acceder a las preferencias del usuario:

[![](tvos9-images/home01.png "La pantalla principal")](tvos9-images/home01.png#lightbox)

El usuario navega por una cuadrícula de iconos de la aplicación mediante gestos táctiles en el Siri remoto con el foco para seleccionar una aplicación e iniciarla. El icono de la aplicación es la primera oportunidad de crear una gran impresión para el usuario potencial y debe comunicar el propósito de la aplicación de un vistazo.

Cada aplicación debe proporcionar una versión pequeña y una grande de su icono de aplicación. El icono pequeño se usará en la pantalla principal de Apple TV cuando se instale la aplicación. La versión grande se usa en el App Store. El icono de la aplicación grande debe imitar la apariencia de la versión del icono pequeño.

Para obtener más información, consulte la documentación sobre [Cómo trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Estante superior

Si el usuario ha colocado la aplicación Xamarin. tvOS en la fila superior de la pantalla de inicio de Apple TV, se mostrará una imagen de estante superior grande cuando el usuario seleccione la aplicación. Esta imagen debe resaltar las características de la aplicación o proporcionar vínculos directos a su contenido.

[![](tvos9-images/topshelf01.png "Estante superior")](tvos9-images/topshelf01.png#lightbox)

La imagen de estante superior se puede proporcionar como un único archivo `.png` o `.lsr` estático, o bien se puede crear dinámicamente en tiempo de ejecución como una sola fila de elementos que pueden recibir el foco.

En lugar de mostrar una imagen de estante superior estática, puede contener una fila dinámica o elementos enfocables o un conjunto dinámico de banners de desplazamiento. Ambos estilos dinámicos le permiten resaltar el contenido proporcionado por la aplicación o saltar a sus características más usadas.

Para obtener más información, consulte la documentación sobre [Cómo trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) y la [referencia de TVServices Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) de Apple para más información sobre cómo agregar una extensión de estante superior a la aplicación para proporcionar contenido dinámico dinámico.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Compilación de aplicaciones para tvOS con Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
