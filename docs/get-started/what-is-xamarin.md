---
title: ¿Qué es Xamarin?
description: En este artículo se presentan Xamarin y las bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: d4abb59cac117314239c669df454a786a3720ff5
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607885"
---
# <a name="what-is-opno-locxamarin"></a>¿Qué es Xamarin?

[![capturas de pantallas del ejemplo [! Operador. Aplicación NO-LOC (Xamarin) en iOS y Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin es una plataforma de código abierto para compilar aplicaciones modernas y de rendimiento para iOS, Android y Windows con .NET. Xamarin es una capa de abstracción que administra la comunicación de código compartido con código de plataforma subyacente. Xamarin se ejecuta en un entorno administrado que proporciona ventajas como la asignación de memoria y la recolección de elementos no utilizados.

Xamarin permite a los desarrolladores compartir un promedio del 90% de la aplicación entre plataformas. Este patrón permite a los desarrolladores escribir toda la lógica de negocios en un solo idioma (o reutilizar el código de aplicación existente), pero conseguir un rendimiento nativo, buscar y sentir en cada plataforma.

Xamarin aplicaciones se pueden escribir en PC o Mac y compilar en paquetes de aplicaciones nativas, como un archivo **. apk** en Android o un archivo **. IPA** en iOS.

> [!NOTE]
> La compilación e implementación de aplicaciones para iOS requiere actualmente una máquina MacOS. Para obtener más información sobre los requisitos de desarrollo, consulte [requisitos del sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-opno-locxamarin-is-for"></a>Quién Xamarin es para

Xamarin es para los desarrolladores con los siguientes objetivos:

- Comparta código, pruebas y lógica de negocios entre plataformas.
- Escriba aplicaciones multiplataforma en C# con Visual Studio.

## <a name="how-opno-locxamarin-works"></a>Cómo funciona Xamarin

![Diagrama de [! Operador. Arquitectura NO-LOC (Xamarin)]](what-is-xamarin-images/xamarin-architecture.png)

En el diagrama se muestra la arquitectura global de una aplicación Xamarin multiplataforma. Xamarin le permite crear una interfaz de usuario nativa en cada plataforma y escribir lógica C# de negocios en que se comparte entre plataformas. En la mayoría de los casos, el 80% del código de la aplicación se compartirá mediante Xamarin.

Xamarin se basa en **mono**, una versión de código abierto de la .NET Framework basada en los estándares ECMA de .net. Mono ha existido casi siempre y cuando el propio .NET Framework y se ejecuta en la mayoría de las plataformas, como Linux, UNIX, FreeBSD y macOS. El entorno de ejecución de mono controla automáticamente las tareas como la asignación de memoria, la recolección de elementos no utilizados y la interoperabilidad con las plataformas subyacentes.

Para obtener más información acerca de la arquitectura específica de la plataforma, consulte [Xamarin. Android](#xamarinandroid) y [Xamarin. iOS](#xamarinios).

### <a name="added-features"></a>Características agregadas

Xamarin combina las capacidades de las plataformas nativas y agrega una serie de características, entre las que se incluyen:

1. **Enlace completo para los SDK subyacentes** : Xamarin contiene enlaces para casi todos los SDK de la plataforma subyacente en iOS y Android. Además, estos enlaces están fuertemente tipados, lo que significa que la navegación y el uso son fáciles y que proporcionan una sólida comprobación de tipos en tiempo de compilación y durante el desarrollo. Los enlaces fuertemente tipados conducen a menos errores en tiempo de ejecución y aplicaciones de mayor calidad.
1. **Objective-C, Java, C e C++ Interop** : Xamarin proporciona funciones para invocar directamente las bibliotecas de Objective-c, Java, c y C++ , lo que le ofrece la posibilidad de usar una amplia gama de código de terceros. Esta funcionalidad permite usar las bibliotecas existentes de iOS y Android escritas en Objective-C, Java o CC++/. Además, Xamarin ofrece proyectos de enlace que permiten enlazar bibliotecas nativas de Objective-C y Java mediante una sintaxis declarativa.
1. **Construcciones de lenguaje modernas** : las aplicaciones de Xamarin están C#escritas en, un lenguaje moderno que incluye importantes mejoras sobre Objective-C y Java, como características del lenguaje dinámico, construcciones funcionales como lambdas, LINQ, programación paralela, genéricos, etc.
1. **Sólida biblioteca de clases base (BCL)** : las aplicaciones Xamarin usan la BCL de .net, una gran colección de clases que tienen características completas y optimizadas, como la eficacia de XML, base de datos, serialización, e/s, cadena y compatibilidad con redes, etc. El C# código existente se puede compilar para su uso en una aplicación, lo que proporciona acceso a miles de bibliotecas que agregan funcionalidad más allá de la BCL.
1. **Entorno de desarrollo integrado (IDE) moderno** : Xamarin usa Visual Studio, un IDE moderno que incluye características como la finalización automática de código, un sofisticado sistema de administración de proyectos y soluciones, una biblioteca de plantillas de proyecto completa, un control de código fuente integrado, etc.
1. **Compatibilidad multiplataforma para dispositivos móviles** : Xamarin ofrece compatibilidad sofisticada multiplataforma para las tres plataformas principales de iOS, Android y Windows. Las aplicaciones se pueden escribir para compartir hasta el 90% del código y Xamarin. Essentials ofrece una API unificada para tener acceso a recursos comunes en las tres plataformas. El código compartido puede reducir significativamente los costos de desarrollo y el tiempo de comercialización de los desarrolladores de dispositivos móviles.

### <a name="opno-locxamarinandroid"></a>Xamarin. Dispositivo

[![[! Operador. NO-LOC (Xamarin)]. Diagrama de arquitectura de Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Xamarin. Las aplicaciones de Android C# se compilan desde en **lenguaje intermedio (IL)** , que luego se compilan **Just-in-Time (JIT)** en un ensamblado nativo cuando se inicia la aplicación. Xamarin. Las aplicaciones de Android se ejecutan en el entorno de ejecución de mono, en paralelo con la máquina virtual de Android Runtime (ART). Xamarin proporciona enlaces .NET a los espacios de nombres Android. * y Java. *. El entorno de ejecución mono llama a estos espacios de nombres a través de contenedores a los que se **puede llamar (MCW) administrados** y proporciona **contenedores de Android Callable (ACW)** a la imagen, lo que permite que ambos entornos invoquen el código entre sí.

Para obtener más información, vea [Xamarin. Arquitectura de Android](~/android/internals/architecture.md).

### <a name="opno-locxamarinios"></a>Xamarin. iOS

[![[! Operador. NO-LOC (Xamarin)]. diagrama de la arquitectura de iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Xamarin. las aplicaciones de iOS son totalmente **preparadas (AOT)** compiladas desde en el código de ensamblado de C# ARM nativo. Xamarin usa **selectores** para exponer Objective-c a C# administrados y **registradores** para C# exponer el código administrado a Objective-c. Los selectores y registradores se denominan colectivamente "enlaces" y permiten que Objective C# -C y se comuniquen.

Para obtener más información, consulte [Xamarin. arquitectura de iOS](~/ios/internals/architecture.md).

### <a name="opno-locxamarinessentials"></a>Xamarin. Essentials

Xamarin. Essentials es una biblioteca que proporciona API multiplataforma para características de dispositivos nativos. Al igual que Xamarin, Xamarin. Essentials es una abstracción que simplifica el proceso de acceso a la funcionalidad nativa. Algunos ejemplos de funcionalidad proporcionada por Xamarin. Entre los aspectos básicos se incluyen:

- Información del dispositivo
- Sistema de archivos
- Acelerómetro
- Marcador de teléfono
- Texto a voz
- Bloqueo de pantalla

Para obtener más información, vea [Xamarin. Aspectos básicos](~/essentials/index.md).

### <a name="opno-locxamarinforms"></a>Xamarin. Forme

Xamarin. Forms es un marco de interfaz de usuario de código abierto. Xamarin. Forms permite a los desarrolladores compilar aplicaciones de iOS, Android y Windows desde un único código base compartido. Xamarin. Forms permite a los desarrolladores crear interfaces de usuario en XAML con código C#subyacente en. Estas interfaces de usuario se representan como controles nativos de rendimiento en cada plataforma. Algunos ejemplos de características proporcionadas por Xamarin. Los formularios incluyen:

- Idioma de la interfaz de usuario de XAML
- Enlace de datos
- Gestos
- Efectos
- Aplicación de estilos

Para obtener más información, vea [Xamarin. Formularios](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Primeros pasos

Las guías siguientes le ayudarán a compilar su primera aplicación mediante Xamarin:

- [Introducción a Xamarin. Forme](~/xamarin-forms/index.yml)
- [Introducción a Xamarin. Dispositivo](~/android/index.yml)
- [Introducción a Xamarin. iOS](~/ios/index.yml)
- [Introducción a Xamarin. Macintosh](~/mac/index.yml)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
