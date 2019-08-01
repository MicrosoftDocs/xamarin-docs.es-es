---
title: Rendimiento de Xamarin.Mac
description: Este documento proporciona algunas consideraciones de rendimiento para aplicaciones de Xamarin.Mac. Analiza la plataforma de destino moderna, el vinculador, el AOT, los delegados, las API de Cocoa para reutilizar las vistas y el código asincrónico.
ms.prod: xamarin
ms.assetid: 54B07DED-FDF2-49B2-A5FB-3A9357E65922
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 76ce6cd3a78e56acb9d77c0c6a4b942e5bdc3f2e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656078"
---
# <a name="xamarinmac-performance"></a>Rendimiento de Xamarin.Mac

## <a name="overview"></a>Información general

Las aplicaciones de Xamarin.Mac son similares a las de Xamarin.iOS, y muchas de las mismas sugerencias de rendimiento son aplicables:

- [Rendimiento de Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)

Sin embargo, hay una serie de sugerencias de macOS específicas que pueden serle de ayuda.

## <a name="prefer-modern-target-framework"></a>Preferir plataformas de destino modernas

Hay varias [plataformas de destino](~/mac/platform/target-framework.md) disponibles en la aplicación de Xamarin.Mac que tienen distintas características y funciones.

Cuando sea posible, utilice las modernas y trabaje con bibliotecas dependientes para agregar compatibilidad. Solo las plataformas de destino modernas permiten la vinculación que puede reducir drásticamente el tamaño de ensamblado. Esto es especialmente importante cuando se habilita AOT, como compilación AOT de ensamblados completas puede generar grandes agrupaciones finales.

## <a name="enable-the-linker"></a>Habilitación del enlazador

El tiempo de inicio, tanto en la carga como en "Just-In-Time" (JIT), escala de forma más o menos lineal el tamaño de los archivos binarios finales. La forma más fácil de mejorar este proceso es quitar el código no alcanzado con el [enlazador](~/mac/deploy-test/linker.md).

Aunque esta sugerencia se aplica principalmente a los usuarios de plataformas de destino modernas, el uso de la [vinculación de plataformas](~/mac/deploy-test/linker.md) también puede proporcionar una mejora limitada del rendimiento.

## <a name="enable-aot-when-appropriate"></a>Habilitación de AOT cuando sea pertinente

Otro aspecto del rendimiento de inicio es la compilación JIT de los ensamblados en código máquina. La compilación Ahead Of Time (AOT) puede reducir de forma significativa el tiempo de inicio, pero presenta una serie de ventajas que se explican en la [documentación de AOT](~/mac/internals/aot.md).

## <a name="ensure-performant-delegates"></a>Garantía del rendimiento de los delegados

Muchas aplicaciones de Xamarin.Mac están centradas en las vistas de Cocoa, como `NSCollectionView`, `NSOutlineView` o `NSTableView`. A menudo, estas vistas se proporcionan mediante las clases `Delegate` y `DataSource` que usted facilita a Cocoa, respondiendo a preguntas sobre lo que hay que mostrar.

Muchos de estos puntos de entrada se invocan con frecuencia. En ocasiones, varias veces por segundo durante un desplazamiento.

Asegúrese de que estas funciones devuelvan valores que se calculen fácilmente o utilicen información ya almacenada en la caché para evitar que la interfaz de usuario se bloquee.

## <a name="use-cocoa-provided-apis-for-reusing-views"></a>Uso de API proporcionadas por Cocoa para reutilizar vistas

Muchas vistas de Cocoa que contienen muchas vistas o celdas secundarias (como `NSCollectionView`, `NSOutlineView` y `NSTableView`) proporcionan API para crear y reutilizar vistas. Dichas vistas crean grupos de elementos compartidos e impiden que haya problemas de rendimiento al desplazarse rápido por las vistas.

## <a name="use-async-and-do-not-block-the-ui"></a>Uso de la asincronía sin bloquear la interfaz de usuario

Las aplicaciones de escritorio a menudo procesan grandes cantidades de datos, y resulta muy fácil bloquear el subproceso de interfaz de usuario a la espera de una operación sincrónica.

Siempre que sea posible, use la [asíncronía](~/cross-platform/platform/async.md) y los subprocesos para que no se bloquee la interfaz de usuario.

Para operaciones de larga ejecución, valore la posibilidad de usar [NSProgressIndicator](https://docs.microsoft.com/samples/xamarin/mac-samples/progressbarexample) u otras opciones indicadas en los documentos de [HIG](https://developer.apple.com/macos/human-interface-guidelines/indicators/progress-indicators/) de Apple para enviar notificaciones a los usuarios.


## <a name="related-links"></a>Vínculos relacionados

- [Rendimiento multiplataforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Rendimiento de Xamarin.iOS](~/ios/deploy-test/performance.md)
