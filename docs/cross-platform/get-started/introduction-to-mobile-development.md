---
title: ¿Qué es Xamarin?
description: En este documento se proporciona una introducción al desarrollo móvil, para lo que se analiza Xamarin, su funcionamiento y las aplicaciones que genera.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
author: conceptdev
ms.author: crdun
ms.date: 07/16/2019
ms.openlocfilehash: 372aee9d48866ac49f34b9550fdfa37b7cc0b646
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526667"
---
# <a name="what-is-xamarin"></a>¿Qué es Xamarin?

Compilar aplicaciones móviles puede ser tan sencillo como abrir el IDE, escribir y probar una aplicación y enviarla a una Tienda de aplicaciones, todo en una misma tarde. O puede ser un proceso muy complicado que conlleva el diseño riguroso por adelantado, pruebas de facilidad de uso, pruebas de control de calidad en miles de dispositivos, un ciclo de vida completo de versión beta y la implementación de varias maneras diferentes.

En este documento se presenta la plataforma Xamarin. Para más información sobre el *proceso* de compilación de aplicaciones móviles desde el diseño hasta las pruebas, consulte [Introducción al ciclo de vida del desarrollo de software móvil](~/cross-platform/get-started/introduction-to-mobile-sdlc.md).

Consulte los [requisitos del sistema](~/cross-platform/get-started/requirements.md#macos-requirements) para confirmar que su sistema es compatible.

## <a name="introduction-to-xamarin"></a>Introducción a Xamarin

Al plantearse cómo compilar aplicaciones para iOS y Android, muchas personas piensan que los lenguajes nativos, Objective-C, Swift, Java y Kotlin son la única opción.

Xamarin ofrece la posibilidad de desarrollar en C# con una biblioteca de clases y un runtime que funciona en muchas plataformas, como iOS, Android y Windows. Además, permite compilar aplicaciones nativas (no interpretadas) que tienen un rendimiento suficiente incluso para juegos exigentes.

Xamarin combina las capacidades de las plataformas nativas y agrega una serie de características propias muy eficaces, entre las que se incluyen las siguientes:

1. **Enlaces completos para los SDK subyacentes**: Xamarin contiene enlaces para casi todos los SDK de plataforma subyacentes en iOS y Android. Además, estos enlaces están fuertemente tipados, lo que significa que la navegación y el uso son fáciles y que proporcionan una sólida comprobación de tipos en tiempo de compilación y durante el desarrollo. Como resultado, se producen menos errores en tiempo de ejecución y aplicaciones de mayor calidad.
1. **Interoperabilidad con Objective-C, Java, C y C++** : Xamarin ofrece funciones para invocar directamente las bibliotecas de Objective-C, Java, C y C++, lo que le permite usar diversos tipos de código de terceros ya creado. Esto le permite aprovechar las ventajas de las bibliotecas existentes de iOS y Android escritas en Objective-C, Java, C o C++. Además, Xamarin ofrece proyectos de enlace que le permiten enlazar fácilmente bibliotecas nativas de Objective-C y Java mediante una sintaxis declarativa.
1. **Construcciones de lenguaje moderno**: las aplicaciones de Xamarin se escriben en C#, un lenguaje moderno que incluye mejoras considerables en Objective-C y Java, como *características de lenguaje dinámico, *construcciones funcionales*, como *lambdas, *LINQ, características de *programación en paralelo*, *genéricos sofisticados, etc.
1. **Increíble biblioteca de clases base (BCL)** : las aplicaciones de Xamarin usan la BCL de .NET, una gran colección de clases con características completas y optimizadas, como una eficaz compatibilidad con XML, bases de datos, serialización, E/S, cadenas y redes, etc. El código C# existente se puede compilar para su uso en una aplicación, lo que proporciona acceso a miles de bibliotecas que le permitirán hacer cosas que no contempla la BCL.
1. **Moderno entorno de desarrollo integrado (IDE)** : Xamarin usa Visual Studio para Mac en macOS y Visual Studio en Windows. Estos IDE modernos incluyen características como la finalización automática de código, un sofisticado sistema de administración de proyectos y soluciones, una biblioteca exhaustiva de plantillas de proyecto, control de código fuente integrado, etc.
1. **Compatibilidad multiplataforma móvil**: Xamarin ofrece una compatibilidad multiplataforma sofisticada con las tres principales plataformas móviles: iOS, Android y Windows. Es posible escribir aplicaciones de modo que compartan hasta el 90 % del código, y la biblioteca de Xamarin.Mobile ofrece una API unificada para tener acceso a recursos comunes de las tres plataformas. Esto puede reducir considerablemente los costos de desarrollo y el tiempo de salida al mercado en el caso de los desarrolladores móviles que se centran en las tres plataformas móviles más populares.

Gracias al conjunto de características eficaces y completas de Xamarin, se llena un vacío para los desarrolladores de aplicaciones que quieren usar un lenguaje y una plataforma modernos para desarrollar aplicaciones móviles multiplataforma.

> [!NOTE]
> Esta serie de introducción se centra en los primeros pasos para compilar aplicaciones iOS y Android. Microsoft ofrece información sobre el [desarrollo de la Plataforma universal de Windows (UWP)](https://docs.microsoft.com/windows/uwp/develop/) para tabletas y equipos de escritorio. Para más información sobre el desarrollo multiplataforma con Xamarin (incluidas las aplicaciones UWP para Windows), lea la guía [Building Cross-Platform Applications (Creación de aplicaciones multiplataforma)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).

## <a name="how-does-xamarin-work"></a>¿Cómo funciona Xamarin?

Xamarin ofrece dos productos comerciales: Xamarin.iOS y Xamarin.Android. Ambos se basan en *Mono*, una versión de código abierto de .NET Framework que tiene en cuenta las normas ECMA de .NET publicadas. Mono existe desde hace casi tanto tiempo como el propio .NET Framework y puede ejecutarse en prácticamente todas las plataformas imaginables, incluido Linux, Unix, FreeBSD y macOS.

En iOS, el compilador *Ahead of time* (*AOT*) de Xamarin compila aplicaciones de Xamarin.iOS directamente en código de ensamblado nativo de ARM. En Android, el compilador de Xamarin compila en *lenguaje intermedio* (*IL*), que es *Just-In-Time* (*JIT*) compilado en ensamblado nativo cuando se inicia la aplicación.

En ambos casos, las aplicaciones de Xamarin usan un tiempo de ejecución que lo controla todo automáticamente, como la asignación de memoria, la recolección de elementos no utilizados, la interoperabilidad de plataforma subyacente, etc.

### <a name="xamariniosdll-and-monoandroiddll"></a>Xamarin.iOS.dll y Mono.Android.dll

Las aplicaciones de Xamarin se compilan en un subconjunto de la BCL de .NET conocido como el perfil móvil de Xamarin. Este perfil se ha creado específicamente para aplicaciones móviles y se ha empaquetado en Xamarin.iOS.dll y Mono.Android.dll (para iOS y Android respectivamente). Se parece mucho a la manera en que las aplicaciones de Silverlight (y Moonlight) se compilan en el perfil de .NET de Silverlight/Moonlight. De hecho, el perfil móvil de Xamarin es equivalente al perfil de Silverlight 4.0 con un conjunto de clases de BCL agregadas de nuevo.

Para obtener una lista completa de las clases y los ensamblados disponibles, consulte [Xamarin.iOS Assembly List](~/cross-platform/internals/available-assemblies.md?context=xamarin/ios) (Lista de ensamblados de Xamarin.iOS) y [Xamarin.Android Assembly List](~/cross-platform/internals/available-assemblies.md?context=xamarin/android) (Lista de ensamblados de Xamarin.Android).

Además de la BCL, estos archivos .dll incluyen contenedores para casi todo el SDK de iOS y Android, lo que permite invocar las API del SDK subyacente directamente desde C#.

### <a name="application-output"></a>Resultado de la aplicación

Cuando se compilan aplicaciones de Xamarin, el resultado es un paquete de aplicación, ya sea un archivo .app en iOS o un archivo .apk en Android. Estos archivos no se distinguen de los paquetes de aplicación compilados con los IDE predeterminados de la plataforma y se implementan de la misma manera.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido lo básico sobre el funcionamiento de Xamarin, el paso siguiente consiste en empezar a compilar una aplicación con una de estas guías:

- [**Introducción a Xamarin.Forms**](~/get-started/index.yml)
- [**Introducción a Xamarin.iOS**](~/ios/get-started/hello-ios/index.md)
- [**Introducción a Xamarin.Android**](~/android/get-started/hello-android/index.md)
- [**Introducción a Xamarin.Mac**](~/mac/get-started/hello-mac.md)
