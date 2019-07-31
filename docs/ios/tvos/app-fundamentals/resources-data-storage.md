---
title: Recursos de tvOS y almacenamiento de datos en Xamarin
description: En este artículo se describe cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación de tvOS compilada con Xamarin. Describe el almacenamiento de datos de iCloud y los recursos a petición.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 4b05d3a1b3a6557fd912ac85023267499c04c262
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645474"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>Recursos de tvOS y almacenamiento de datos en Xamarin

_En este artículo se explica cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación Xamarin. tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>Limitaciones de los recursos de tvOS

A diferencia de los dispositivos iOS, el nuevo Apple TV proporciona almacenamiento local y persistente extremadamente limitado para los datos o aplicaciones tvOS. En el caso de elementos muy pequeños (como las preferencias de usuario), la aplicación tvOS `NSUserDefaults` todavía tiene acceso a con un [límite de 500 KB de datos](https://forums.developer.apple.com/message/50696#50696). Sin embargo, si la aplicación de Xamarin. tvOS necesita conservar grandes cantidades de información, debe almacenar y recuperar los datos de [iCloud](#iCloud-Data-Storage).

Además, tvOS limita el tamaño de una aplicación Apple TV a 200 MB. Si la aplicación requiere recursos que superen este tamaño, deberán empaquetarse y cargarse mediante [recursos a petición](#On-Demand-Resources) (hasta 2 GB adicionales). Dadas estas limitaciones, es fundamental que el tiempo se descargue correctamente para proporcionar la mejor experiencia a los usuarios de la aplicación. Para obtener más información, consulte la [Guía de recursos a petición](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)de Apple.

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Descargas no persistentes

A cada aplicación tvOS se le proporciona un directorio de caché temporal en el que se descargan sus recursos y recursos adicionales. Este directorio se conservará siempre y cuando la aplicación se siga ejecutando. Sin embargo, como Apple TV necesita liberar espacio para otras aplicaciones o uso del sistema, se puede eliminar esta caché.

Como resultado, la aplicación no puede confiar en que el contenido descargado previamente esté disponible la próxima vez que se inicie. La aplicación Xamarin. tvOS siempre debe comprobar la existencia de los recursos necesarios y descargarlos según sea necesario.

> [!IMPORTANT]
> Aunque tiene la posibilidad de descargar otros recursos y recursos según sea necesario, Apple advierte de que se consume todo el espacio en la memoria caché de la aplicación, ya que puede provocar resultados imprevisibles.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Administrar recursos

Como se indicó anteriormente, debido al almacenamiento limitado y no persistente de la información disponible para las aplicaciones de tvOS, estas restricciones requieren una planeación cuidadosa para crear una excelente experiencia de usuario para la aplicación Xamarin. tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Almacenamiento de datos de iCloud

Dado que el almacenamiento en Apple TV es limitado, no solo existe un almacenamiento local y persistente muy limitado, la aplicación no tiene ninguna garantía de que toda la información que descargó anteriormente estará disponible la próxima vez que se ejecute.

Como resultado, la aplicación Xamarin. tvOS debe almacenar los datos de usuario en un almacén de datos de iCloud. Apple proporciona dos opciones de almacenamiento de datos basadas en iCloud para las aplicaciones de tvOS:

- **almacenamiento de clave-valor de icloud (KVS)** : para pequeñas piezas de información (menos de 1 MB) que puede necesitar la aplicación (como las preferencias de usuario), puede usar el almacenamiento de ICLOUD en KVS. los datos de iCloud KVS se sincronizan automáticamente con la nube y todos los dispositivos del usuario ejecutan la misma aplicación. Para obtener más información, consulte la sección sobre el [almacenamiento de valor de clave](~/ios/data-cloud/introduction-to-icloud.md) de nuestro documento de [Introducción a ICloud](~/ios/data-cloud/introduction-to-icloud.md) o el diseño de Apple [para la información de clave-valor en la documentación de icloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) .
- **CloudKit** : para el almacenamiento de datos de mayor tamaño (más de 1 MB), use CloudKit Framework de Apple. A diferencia del almacenamiento de iCloud KVS, los datos de CloudKit se pueden compartir entre todos los usuarios de la aplicación (así como ser privados de un solo usuario). Proporcione más información, consulte nuestra [Introducción a](~/ios/data-cloud/intro-to-cloudkit.md) la documentación de CloudKit o la [Inicio rápido de CloudKit](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)de Apple.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Recursos a petición

Los recursos a petición proporcionan contenido y recursos de la aplicación (independientes del lote de aplicaciones) que se hospedan en la tienda de aplicaciones y se descargan según lo requiera la aplicación. Se pueden atender hasta 2 GB de datos adicionales mediante recursos a petición. Permiten que la descarga inicial de la aplicación sea más pequeña (las aplicaciones tvOS se limitan a un máximo de 200 MB) y, al mismo tiempo, proporcionan recursos enriquecidos según sea necesario.

Cuando una aplicación tvOS solicita recursos a petición, el sistema administrará automáticamente la descarga y el almacenamiento de este contenido en el directorio de la memoria caché de la aplicación. La aplicación debe esperar a que se descargue y esté disponible este contenido para poder continuar.

Estos recursos se pueden seguir almacenando en la memoria caché de Apple TV a lo largo de varios inicios de la aplicación, lo que acelera el ciclo de inicio. Sin embargo, la aplicación no puede confiar en que el contenido descargado previamente esté disponible la próxima vez que se inicie. Vea la sección de [descargas no persistentes](#Non-Persistent-Downloads) anterior para obtener más detalles.

Xcode se usa para crear agrupaciones de contenido relacionado (por ejemplo, todos los recursos para el nivel de juego 2) asociadas a una etiqueta de recurso. Posteriormente, la aplicación solicitará el recurso a petición especificando esta etiqueta de recurso. La aplicación debe presentar una interfaz de usuario al usuario que indique que se está descargando el contenido. Para obtener más información, consulte la [Guía de recursos a petición](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083)de Apple.

> [!IMPORTANT]
> Se debe tener cuidado para lograr el equilibrio adecuado entre el número de veces que la aplicación tiene que descargar recursos a petición y el tamaño de las descargas individuales. El usuario puede resultar frustrado con la aplicación si el juego se interrumpe constantemente para descargar contenido nuevo o si una única descarga tarda demasiado tiempo.




<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se han tratado las limitaciones de tamaño, recursos y almacenamiento de datos colocadas en una aplicación Xamarin. tvOS por el sistema tvOS. Ha presentado opciones para solucionar estas limitaciones y sugerencias para crear una experiencia de usuario excelente para la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
