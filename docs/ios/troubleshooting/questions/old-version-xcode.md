---
title: ¿Puedo usar una versión anterior de Xcode o Xamarin. iOS?
description: En esta guía se describen los problemas relacionados con el uso de versiones anteriores de Xamarin. iOS o Xcode (que la versión estable actual).
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 6c6a0491f7989f2f76afabc3412e38be74a06da4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431572"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>¿Puedo usar una versión anterior de Xcode o Xamarin. iOS?

La documentación de Xamarin presupone el uso de las últimas Xamarin. iOS y Xcode, que se recomienda. Sin embargo, algunos clientes prefieren usar Xamarin. iOS y/o Xcode anteriores, y le gustaría obtener detalles sobre las consecuencias.

Las notas de la versión contienen la siguiente ADVERTENCIA:

> [!WARNING]
> **Uso de una versión anterior de Xcode**
>
> Con frecuencia, es posible usar una versión anterior de Xcode (que la mencionada en los [requisitos](/xamarin/ios/release-notes/12/12.8#requirements)anteriores), pero es posible que algunas características no estén disponibles. También algunas limitaciones pueden requerir soluciones alternativas, por ejemplo:
>
> - El registrador estático requiere archivos de encabezado de Xcode para compilar aplicaciones, lo que provoca [`MT0091`](../mtouch-errors.md#MT0091) o [`MT4109`](../mtouch-errors.md#MT4109) errores si faltan las API. En la mayoría de los casos, la habilitación de Managed linker le ayudará (quitando la API).
> - Las compilaciones de Bitcode (para tvOS y watchos) pueden producir un error en el envío a la tienda de aplicaciones a menos que se use una cadena de herramientas de Xcode 9.0 +.

## <a name="further-information"></a>Más información

Microsoft recomienda encarecidamente usar la versión más reciente de Xcode y la versión más reciente de Xamarin. iOS al desarrollar y enviar aplicaciones. Apple requiere el uso de Xcode más reciente al enviar aplicaciones.

Tenga en cuenta que el uso de la versión más reciente de Xcode no impide que la aplicación tenga como destino versiones anteriores de iOS. Las versiones de iOS que se admiten se basan en la entrada **info. plist** y las API que usa la aplicación.

Es posible instalar varias versiones de Xcode en paralelo, con nombres diferentes como **Xcode101. app** y **Xcode102. app**. Si usa varias versiones, asegúrese de establecer el Xcode activo en [Visual Studio para Mac configuración](~/ios/troubleshooting/questions/ios-sdk.md) y con la herramienta de [`xcode-select`](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [línea de comandos](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_).

Sin embargo, algunas circunstancias poco frecuentes pueden requerir el uso de componentes anteriores. En esta documentación se describen los desafíos generales que puede encontrar al usar versiones anteriores a la más reciente.

Sin embargo, cada versión de Apple es única, por lo que puede encontrar otros errores que no se documentan aquí.

En ocasiones, estos desafíos no son triviales, por lo que, siempre que sea posible, puede usar la configuración admitida de la versión más reciente de Xcode y Xamarin. iOS más reciente.

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>Uso de un antiguo Xamarin. iOS con Xcode antiguo

No es posible actualizar Xamarin. iOS y Xcode, al menos durante un período de tiempo. El límite es que, en algún momento, Apple necesitará una versión mínima de Xcode para enviar las aplicaciones. En este momento debe actualizar todos los componentes (macOS, Xcode y Xamarin. iOS) a las versiones más recientes (o a la nueva versión mínima de Xcode que requiere Apple y la versión de Xamarin. iOS coincidente).

Generalmente, es más fácil actualizar gradualmente y mantenerse al día con los pequeños cambios. En el caso de los proyectos de gran tamaño en los que las actualizaciones pueden ser más difíciles de usar, mantener un espacio de trabajo conocido puede ser un buen compromiso.

## <a name="use-of-new-xamarinios-with-older-xcode"></a>Uso del nuevo Xamarin. iOS con Xcode antiguo

Xamarin. iOS en general admite versiones anteriores de Xcode siempre que sea razonablemente posible. Algunos posibles desafíos son:

- Las versiones más recientes de Xamarin. iOS pueden admitir algunas características y API que no están presentes en la Xcode seleccionada. 
- El **registrador estático** requiere archivos de encabezado de Xcode para compilar aplicaciones, lo que provoca [`MT0091`](~/ios/troubleshooting/mtouch-errors.md#MT0091) o [`MT4109`](~/ios/troubleshooting/mtouch-errors.md#MT4109) errores si faltan las API.
  - En la mayoría de los casos, la habilitación de Managed linker le ayudará (quitando los enlaces administrados de la nueva API) si no se usa.
- Las compilaciones de Bitcode (para tvOS y watchos) pueden producir un error en el envío a la tienda de aplicaciones a menos que se use una cadena de herramientas de Xcode 9.0 +.

## <a name="use-of-new-xcode-with-older-xamarinios"></a>Uso de Xcode nuevo con Xamarin. iOS anterior

Este caso de uso es significativamente más difícil, ya que Xamarin. iOS no puede predecir los requisitos cambiantes de Xcode nuevo. Las actualizaciones de macOS también pueden presentar problemas y sin revisiones de compatibilidad muchas partes de Xamarin. iOS podrían verse afectadas. 

Hay varias áreas posibles en las que las cosas pueden ir erróneas, entre las que se incluyen:

- Incompatibilidades con `mlaunch` :
  - Es posible que la compatibilidad con el simulador no funcione correctamente (o en absoluto)
  - Es posible que la compatibilidad con dispositivos no funcione correctamente (o en absoluto)
- Compatibilidad desconocida para `mtouch` 
  - No se admiten nuevos marcos de trabajo
  - No se admiten nuevos derechos
  - No se admiten herramientas nuevas o actualizadas
    - Esto también puede afectar a la firma de código

### <a name="new-appstore-submission-rules"></a>Nuevas reglas de envío de AppStore

Apple se reserva el derecho a actualizar las reglas de envío de AppStore en cualquier momento. Estos cambios de reglas solo se anuncian a veces de antemano. Algunos de estos cambios requieren que los cambios en las herramientas de soporte técnico, lo que requeriría un componente de Xamarin. iOS actualizado.

Además de los cambios en las reglas, Apple suele agregar validaciones adicionales a las aplicaciones enviadas o estrecha las existentes. Algunos de ellos requieren cambios en nuestras herramientas (por ejemplo, un nuevo símbolo de lista negra). Muchos de ellos se encuentran en primer lugar por clientes que envían, ya que no hay ningún anuncio (o lista) de las reglas.

## <a name="summary"></a>Resumen

Siempre que sea posible, juegue con seguridad siguiendo las instrucciones de Apple y desarrolle y envíe con la versión más reciente de Xcode Publicada en el App Store.

A su vez, use la versión más reciente de Xamarin. iOS publicada. Esto hará un seguimiento de las correcciones más recientes que pueden afectar a las aplicaciones enviadas y cumplir los cambios de reglas más recientes.

Cuando eso no sea factible, considere la posibilidad de usar una versión anterior de Xcode y Xamarin. iOS que coincida. Esto puede funcionar durante un tiempo, pero en algún momento Apple insiste en las nuevas herramientas, por lo que debe planearlo en consecuencia.