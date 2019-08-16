---
title: Procesamiento en segundo plano en Xamarin. iOS
description: El procesamiento en segundo plano o el fondo es el proceso de permitir que las aplicaciones realicen tareas en segundo plano mientras otra aplicación se está ejecutando en primer plano. Esta guía sirve como introducción al procesamiento en segundo plano en iOS.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: 2dfa88ae349a792c524b17a25d454dae86575756
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521381"
---
# <a name="backgrounding-in-xamarinios"></a>Procesamiento en segundo plano en Xamarin. iOS

_El procesamiento en segundo plano o el fondo es el proceso de permitir que las aplicaciones realicen tareas en segundo plano mientras otra aplicación se está ejecutando en primer plano. Esta guía sirve como introducción al procesamiento en segundo plano en iOS._

El procesamiento en segundo plano en aplicaciones móviles es fundamentalmente diferente del concepto tradicional de multitarea en el escritorio. Los equipos de escritorio tienen una gran variedad de recursos disponibles para una aplicación, como el estado real de la pantalla, la energía y la memoria. Las aplicaciones pueden ejecutarse en paralelo y tener un rendimiento y ser utilizable. En un dispositivo móvil, los recursos son mucho más limitados. Es difícil Mostrar más de una aplicación en una pantalla pequeña y ejecutar varias aplicaciones a gran velocidad agotaría la batería. El fondo es un compromiso constante entre dar a las aplicaciones los recursos para ejecutar las tareas en segundo plano que requieren y mantener la aplicación en primer plano y el dispositivo que responde. Tanto iOS como Android tienen disposiciones para el fondo, pero lo administran de manera muy diferente.

En iOS, el fondo se reconoce como un estado de aplicación y las aplicaciones se mueven dentro y fuera del estado en segundo plano en función del comportamiento de la aplicación y del usuario. iOS también ofrece varias opciones para conectar una aplicación para que se ejecute en segundo plano, lo que incluye pedir al sistema operativo el tiempo necesario para completar una tarea importante, funcionando como un tipo de aplicación conocida necesaria para el segundo plano y actualizar el contenido de una aplicación en la forma designada. intervalos.

En esta guía y los tutoriales que lo acompañan, vamos a aprender a realizar tareas de aplicación en segundo plano. Trataremos los conceptos clave y los procedimientos recomendados y, a continuación, le guiaremos en la creación de una aplicación real que recibe actualizaciones de ubicación en segundo plano.

## <a name="contents"></a>Contenido

1. [Introducción al procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [Demostración del ciclo de vida de la aplicación](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [Técnicas de procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [Tutoriales: procesamiento en segundo plano en iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [Guía del procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Resumen

En esta guía se presentaron las distintas formas de realizar el procesamiento en segundo plano en iOS. Hemos tratado los Estados de las aplicaciones de iOS y hemos examinado los reproducciones de roles en el ciclo de vida de la aplicación de iOS. Además, hemos aprendido cómo podríamos registrar tareas individuales o aplicaciones completas para que funcionen en segundo plano en iOS. Por último, hemos reforzado nuestro conocimiento del fondo en iOS mediante la creación de aplicaciones que realizan actualizaciones en segundo plano.



## <a name="related-links"></a>Vínculos relacionados

- [Procesamiento en segundo plano en Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
- [Ubicación (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Transferencia de fondo simple (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [Ejecución en segundo plano de iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
