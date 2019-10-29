---
title: Unificación de componentes de Google Play Services y NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 100ef7ffd7e05db0ed8b2af6b9990fc3a0ac1fa9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014148"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificación de componentes de Google Play Services y NuGet

## <a name="history"></a>Historial

Ha usado varios componentes de Google Play Services y paquetes NuGet:

- Google Play Services (Froyo)
- Google Play Services (Gingerbread)
- Google Play Services (ICS)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

En realidad, Google solo distribuye dos archivos. jar para Google Play Services:

- `google-play-services-froyo.jar`
- `google-play-services.jar`

La discrepancia existía porque nuestras herramientas no indican `aapt.exe` cuál era el nivel máximo de API de recursos que se va a usar para una aplicación determinada. Esto significaba que se recibieron errores de compilación si se intentó usar el enlace de Google Play Services (KitKat) en un nivel de API más bajo como Gingerbread.

## <a name="unifying-google-play-services"></a>Unificar Google Play Services

En las versiones más recientes de Xamarin. Android, ahora se indica `aapt.exe` qué versión máxima de recursos usar, por lo que este problema desaparece.

Esto significa que no hay ningún motivo real para tener paquetes independientes para Gingerbread/ICS/JellyBean/KitKat (sin embargo, todavía necesitamos un enlace independiente para Froyo, ya que es un archivo. jar diferente).

Para facilitar la tarea a los desarrolladores, ahora hemos unificado nuestros componentes y paquetes de NuGet en dos:

- Google Play Services (Froyo) (enlaza `google-play-services-froyo.jar`)
- Google Play Services (enlaza `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>¿Cuál debe usarse?

En casi todos los casos, se debe utilizar Google Play Services. La única razón para usar el paquete de (Froyo) es si se dirige activamente a Froyo. El único motivo por el que existe este archivo. jar independiente de Google es que Froyo se encuentra en un porcentaje pequeño de dispositivos, ellos mismos han decidido dejar de admitirlo, por lo que este archivo. jar es una instantánea inmovilizada y no compatible de Google Play Services.

### <a name="note-about-gingerbread"></a>Nota sobre Gingerbread

Gingerbread no admite fragmentos de forma predeterminada y, por este motivo, algunas de las clases del enlace no se podrán usar en una aplicación en tiempo de ejecución en un dispositivo Gingerbread. Las clases como `MapFragment` no funcionarán en Gingerbread y su variante de compatibilidad debe usarse en su lugar `SupportMapFragment`. Es el desarrollador quien sabe cuál debe usar. Google indica esta incompatibilidad en su documentación de Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>¿Qué ocurre con los componentes o NuGet antiguos?

Dado que ya no son necesarios, hemos deshabilitado o eliminado de la lista los siguientes componentes o paquetes Nuget:

- Google Play Services (Gingerbread)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

El nombre del componente de _Google Play Services (ICS)_ existente/Nuget ha cambiado a _Google Play Services_ y se mantendrá actualizado en el futuro. Todos los proyectos que hacen referencia a uno de los paquetes deshabilitados o delistados deben actualizarse para usar este.

Los componentes deshabilitados todavía existen y deben restaurarse para los proyectos a los que todavía se hace referencia, para evitar que se interrumpan. De igual forma, los paquetes NuGet que se han deslistado siguen existiendo y se pueden restaurar. No se actualizarán en el futuro.
