---
title: Introducción a macOS Sierra
description: En este artículo se presentan todas las API y características nuevas y modificadas disponibles en macOS Sierra para desarrolladores de Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 7d758d4cb9906542d388f3ced185a0d0bcba5fcd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651529"
---
# <a name="introduction-to-macos-sierra"></a>Introducción a macOS Sierra

Con el nuevo macOS Sierra, el desarrollador puede aprovechar las nuevas API que permiten que el usuario final interactúe con sus aplicaciones y sitios web de maneras no disponibles anteriormente. Por ejemplo, Apple ahora permite que los sitios web ofrezcan a los clientes la opción de pagar de forma segura a través de Apple Pay y las mejoras realizadas en el marco de metal aumentan el potencial de gráficos y informática de una aplicación. 

Para obtener más información sobre macOS Sierra, consulte la documentación de [MacOS + aplicaciones](https://developer.apple.com/macos/) de Apple.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>Novedades de macOS Sierra

Apple ha agregado varias API y servicios nuevos en macOS Sierra junto con muchas mejoras en las características existentes, entre las que se incluyen:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Apple File System

Con macOS Sierra, Apple ha lanzado el nuevo sistema de archivos de Apple como sistema de archivos moderno para iOS, macOS, tvOS y watchos. El sistema de archivos de Apple se optimizó para el almacenamiento de Flash y SSD y proporciona las siguientes características: cifrado seguro, metadatos de copia en escritura, uso compartido de espacio, clonación de archivos y directorios, instantáneas, cambio rápido de tamaño de directorios y primitivas de almacenamiento de seguridad atómica.

Para obtener más información, consulte la [Guía del sistema de archivos Apple](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)de Apple.

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Apple Pay mejoras

Apple ha realizado varias mejoras en Apple Pay en macOS Sierra que permiten al usuario realizar pagos seguros desde sitios Web.

Con macOS Sierra, se han agregado varias API nuevas que funcionan con macOS Sierra, iOS y watchos para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

macOS Sierra incluye el nuevo marco de JavaScript de ApplePay que permite al desarrollador incorporar Apple Pay directamente en sitios web basados en iOS y macOS Safari. En el caso de los sitios web que admiten Apple Pay, el usuario puede autorizar el pago mediante su iPhone o Apple Watch.

Para obtener más información, consulte la referencia de [.NET Framework de APPLEPAY JS](https://developer.apple.com/reference/applepayjs) .

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Creación de aplicaciones modernas de macOS

Aplicaciones modernas de macOS como el explorador Web de Safari de Apple, páginas procesador de textos de páginas y números de la hoja de distribución usan muchas nuevas tecnologías para presentar una interfaz de usuario unificada y contextual que se encuentra fuera de los elementos de interfaz de usuario tradicionales, como los paneles flotantes y varios abiertos. Windows.

[![Ejemplo de una ventana de Mac con pestañas](images/content08.png)](images/content08.png#lightbox)

En la guía de [creación de aplicaciones MacOS modernas](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) se describen varias sugerencias, características y técnicas que un desarrollador puede usar para compilar una aplicación moderna de MacOS en Xamarin. Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Uso compartido de datos CloudKit

El marco de trabajo de CloudKit se ha ampliado en macOS Sierra para permitir que el usuario comparta de forma rápida y sencilla registros o conjuntos de registros de sus bases de datos de iCloud privadas.

CloudKit proporciona una interfaz de usuario completa para enviar y aceptar invitaciones de registros compartidos, y el usuario tiene un control completo de lectura y escritura sobre las personas que tienen acceso a los registros.

Para obtener más información, consulte referencia de [CloudKit Framework](https://developer.apple.com/reference/clockkit) de Apple y [referencia de Framework de CloudKit JS](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Compatibilidad con las extensiones de aplicación Safari

Las extensiones de aplicación Safari permiten a la aplicación ampliar el comportamiento del explorador Web Safari mientras están estrechamente integrados con macOS Sierra. Dado que las extensiones de aplicación de la Safari de macOS funcionan de manera similar a las extensiones de aplicación Safari de iOS, son fáciles de migrar de un sistema a otro.

Para obtener más información, consulte la guía de programación de la [extensión de aplicación Safari](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)de Apple.

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Mejoras en la seguridad y la privacidad

Apple ha realizado varias mejoras en la seguridad y la privacidad en macOS Sierra que ayudarán a la aplicación a mejorar la seguridad de la aplicación y a garantizar la privacidad del usuario final, que incluye lo siguiente:

- La nueva `NSAllowsArbitraryLoadsInWebContent` clave se puede Agregar al archivo de `Info.plist` la aplicación y permitirá que las páginas web se carguen correctamente, mientras que la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación.
- La API de Common Data Security Architecture (CDSA) ha quedado en desuso y debe reemplazarse por la API de SecKey para generar claves asimétricas.
- Para todas las conexiones SSL/TLS, el cifrado simétrico RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación deje de usar la criptografía SHA-1 y 3DES lo antes posible.
- Dado que el nuevo portapapeles en iOS 10 y macOS Sierra permite al usuario copiar y pegar entre dispositivos, la API se ha expandido para permitir que un portapapeles se limite a un dispositivo específico y que tenga la marca de tiempo para que se borre automáticamente en un momento dado. Además, los portapapeles con nombre ya no se conservan y deben reemplazarse por los contenedores de la mesa de pegada.
- Si la aplicación tiene acceso a datos protegidos (como el calendario del usuario), _debe_ declarar ese intento con la clave de valor de cadena de propósito correcto `Info.plist` en su`NSCalendarUsageDescription` archivo (en el caso del calendario).
- Las aplicaciones firmadas para desarrolladores que no se entregan a través de Mac App Store ahora pueden aprovechar las ventajas de CloudKit, la cadena de claves de iCloud, la unidad iCloud, las notificaciones de entrega remotas, los derechos de MapKit y VPN.
- macOS Sierra ya no admite la entrega de código externo o datos junto con la aplicación de firma de código en su imagen de archivo zip o de disco sin signo, ya que la ruta de acceso en tiempo de ejecución no se conoce antes del tiempo de ejecución.

Además, las aplicaciones que se ejecutan en MacOS Sierra (o posterior) deben declarar estáticamente su intención de acceder a características específicas o información de usuario especificando una o `Info.plist` más claves específicas de la privacidad en sus archivos que expliquen al usuario por qué la aplicación quiere obtener acceso.

Como macOS Sierra comparte estos cambios con iOS 10, consulte nuestra guía de [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) de iOS 10 para obtener más información.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Compatibilidad con la extensión de controlador de tarjeta inteligente

Con MacOS Sierra, la aplicación puede crear `NSExtension` controladores de tarjetas inteligentes basados en que permitan el acceso de solo lectura al contenido de determinados tipos de tarjetas inteligentes. Esta información se presenta en la cadena de claves del sistema (reemplazando el método de arquitectura de seguridad de datos común en desuso).

para más información, vea la referencia de [CryptoTokenKit Framework](https://developer.apple.com/reference/cryptotokenkit)de Apple.

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registro Unificado

El Registro Unificado proporciona a la aplicación una única API para la mensajería eficaz en todos los niveles del sistema. Con el Registro Unificado, la aplicación tiene un control exhaustivo de varios niveles de registro que incluyen controles de privacidad y seguimiento de actividades para una depuración más sencilla. 

El registro proporciona la correlación automática de mensajes cuando se usan conjuntamente el seguimiento y el registro de la actividad.

macOS Sierra incluye una nueva aplicación de consola (en aplicaciones y utilidades) que puede mostrar datos de registro de varios orígenes, incluidos los dispositivos conectados. También admite búsquedas con tokens y guardadas y muestra las conexiones entre los mensajes relacionados entre varios procesos.

Además, los mensajes de registro se pueden ver y mantener mediante herramientas de línea de comandos.

Para obtener más información, consulte la [referencia de registro](https://developer.apple.com/documentation/os/logging)de Apple.

<a name="Wide-Color" />

### <a name="wide-color"></a>Color amplio

macOS Sierra amplía la compatibilidad con los formatos de píxeles de rango extendido y los espacios de colores de amplia gama en todo el sistema, incluidos los marcos como los gráficos principales, la imagen principal, el metal y el AVFoundation. La compatibilidad con dispositivos con pantallas de color ancho se facilita aún más proporcionando este comportamiento en toda la pila de gráficos.

Además, `AppKit` se ha modificado para que funcione en el nuevo **sRGB** ColorSpace ampliado, lo que facilita la combinación de colores en gamas de colores anchos sin pérdida significativa de rendimiento.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores anchos:

- `NSColor`ahora usa el espacio de color sRGB y dejará de fijar valores en `0.0` el `1.0` intervalo para. Si la aplicación se basa en el comportamiento de la abrazadera anterior, deberá modificarla para macOS Sierra.
- Cuando se usa una API de bajo nivel como gráficos principales o metal para proporcionar el procesamiento de imágenes, la aplicación debe usar un espacio de colores extendido y un formato de píxel que admita valores de punto flotante de 16 bits. Cuando sea necesario, la aplicación tendrá que fijar manualmente los valores de componente de color.
- Los gráficos principales, los sombreadores de imagen principal y de rendimiento de metal proporcionan nuevos métodos para la conversión entre los dos espacios de color.

Para obtener más información, consulte la guía [de introducción a la amplia gama de colores](~/ios/platform/wide-color.md) .

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Cambios adicionales en el marco de trabajo

Además de los principales cambios en el marco de trabajo y las adiciones enumeradas anteriormente, Apple ha realizado muchos cambios en el marco de trabajo más pequeños en macOS Sierra.

Para obtener más información, consulte nuestra guía [adicional de cambios](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) en el marco de trabajo.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Interfaces API desusadas

Las siguientes API están desusadas en macOS Sierra:

- Ya no se admite el sistema de archivos HFS estándar.

Consulte la documentación sobre diferencias de la [API de MacOS v 10.12](https://developer.apple.com/library/archive/releasenotes/General/APIDiffsMacOS10_12/index.html) de Apple para obtener una lista completa de los cambios y las desuso.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novedades de macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
