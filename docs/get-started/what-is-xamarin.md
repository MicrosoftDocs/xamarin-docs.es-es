---
title: ¿Qué es Xamarin?
description: En este artículo se presentan Xamarin y las bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
ms.openlocfilehash: 34763804e9833224721ea32f9c7e6200dd5faba7
ms.sourcegitcommit: ba83c107c87b015dbcc9db13964fe111a0573dca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2020
ms.locfileid: "75607885"
---
# <a name="what-is-xamarin"></a>¿Qué es Xamarin?

[![capturas de pantallas de la aplicación Xamarin de ejemplo en iOS y Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin es una plataforma de código abierto para compilar aplicaciones modernas y de rendimiento para iOS, Android y Windows con .NET. Xamarin es una capa de abstracción que administra la comunicación de código compartido con código de plataforma subyacente. Xamarin se ejecuta en un entorno administrado que proporciona ventajas como la asignación de memoria y la recolección de elementos no utilizados.

Xamarin permite a los desarrolladores compartir un promedio del 90% de la aplicación entre plataformas. Este patrón permite a los desarrolladores escribir toda la lógica de negocios en un solo idioma (o reutilizar el código de aplicación existente), pero conseguir un rendimiento nativo, buscar y sentir en cada plataforma.

Las aplicaciones de Xamarin se pueden escribir en PC o Mac y compilar en paquetes de aplicaciones nativas, como un archivo **. apk** en Android o un archivo **. IPA** en iOS.

> [!NOTE]
> La compilación e implementación de aplicaciones para iOS requiere actualmente una máquina MacOS. Para obtener más información sobre los requisitos de desarrollo, consulte [requisitos del sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>Personas para Xamarin

Xamarin es para desarrolladores con los siguientes objetivos:

- Comparta código, pruebas y lógica de negocios entre plataformas.
- Escriba aplicaciones multiplataforma en C# con Visual Studio.

## <a name="how-xamarin-works"></a>Cómo funciona Xamarin

![Diagrama de la arquitectura de Xamarin](what-is-xamarin-images/xamarin-architecture.png)

En el diagrama se muestra la arquitectura general de una aplicación Xamarin multiplataforma. Xamarin le permite crear una interfaz de usuario nativa en cada plataforma y escribir lógica C# de negocios en que se comparte entre plataformas. En la mayoría de los casos, el 80% del código de la aplicación se compartirá con Xamarin.

Xamarin se basa en **mono**, una versión de código abierto de la .NET Framework en función de los estándares de .net ECMA. Mono ha existido casi siempre y cuando el propio .NET Framework y se ejecuta en la mayoría de las plataformas, como Linux, UNIX, FreeBSD y macOS. El entorno de ejecución de mono controla automáticamente las tareas como la asignación de memoria, la recolección de elementos no utilizados y la interoperabilidad con las plataformas subyacentes.

Para obtener más información sobre la arquitectura específica de la plataforma, consulte [Xamarin. Android](#xamarinandroid) y [Xamarin. iOS](#xamarinios).

### <a name="added-features"></a>Características agregadas

Xamarin combina las capacidades de las plataformas nativas y agrega una serie de características, entre las que se incluyen:

1. **Enlace completo para los SDK subyacentes** : Xamarin contiene enlaces para casi todos los SDK de la plataforma subyacente en iOS y Android. Además, estos enlaces están fuertemente tipados, lo que significa que la navegación y el uso son fáciles y que proporcionan una sólida comprobación de tipos en tiempo de compilación y durante el desarrollo. Los enlaces fuertemente tipados conducen a menos errores en tiempo de ejecución y aplicaciones de mayor calidad.
1. **Objective-C, Java, C e C++ Interop** : Xamarin ofrece recursos para invocar directamente las bibliotecas de Objective-c, Java, c y C++ , lo que le ofrece la posibilidad de usar una amplia gama de código de terceros. Esta funcionalidad permite usar las bibliotecas existentes de iOS y Android escritas en Objective-C, Java o CC++/. Además, Xamarin ofrece proyectos de enlace que permiten enlazar bibliotecas nativas de Objective-C y Java mediante una sintaxis declarativa.
1. **Construcciones de lenguaje moderno** : las aplicaciones de Xamarin están C#escritas en, un lenguaje moderno que incluye importantes mejoras sobre Objective-C y Java, como características del lenguaje dinámico, construcciones funcionales como lambdas, LINQ, programación paralela, genéricos, etc.
1. **Sólida biblioteca de clases base (BCL)** : las aplicaciones de Xamarin usan la BCL de .net, una gran colección de clases que tienen características completas y optimizadas, como la eficacia de XML, base de datos, serialización, e/s, cadena y compatibilidad de red, etc. El C# código existente se puede compilar para su uso en una aplicación, lo que proporciona acceso a miles de bibliotecas que agregan funcionalidad más allá de la BCL.
1. **Entorno de desarrollo integrado (IDE) moderno** : Xamarin usa Visual Studio, un IDE moderno que incluye características como la finalización automática de código, un sofisticado sistema de administración de proyectos y soluciones, una biblioteca de plantillas de proyecto completa, un control de código fuente integrado, etc.
1. **Compatibilidad multiplataforma para dispositivos móviles** : Xamarin ofrece un sofisticado soporte multiplataforma para las tres plataformas principales de iOS, Android y Windows. Las aplicaciones se pueden escribir para compartir hasta el 90% de su código y Xamarin. Essentials ofrece una API unificada para acceder a recursos comunes en las tres plataformas. El código compartido puede reducir significativamente los costos de desarrollo y el tiempo de comercialización de los desarrolladores de dispositivos móviles.

### <a name="xamarinandroid"></a>Xamarin.Android

[diagrama de arquitectura de Xamarin. Android ![](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Las aplicaciones de Xamarin. Android C# se compilan desde en **lenguaje intermedio (IL)** , que luego se compilan **Just-in-Time (JIT)** en un ensamblado nativo cuando se inicia la aplicación. Las aplicaciones de Xamarin. Android se ejecutan en el entorno de ejecución de mono, en paralelo con la máquina virtual en tiempo de ejecución (ART) de Android. Xamarin proporciona enlaces .NET a los espacios de nombres Android. * y Java. *. El entorno de ejecución mono llama a estos espacios de nombres a través de contenedores a los que se **puede llamar (MCW) administrados** y proporciona **contenedores de Android Callable (ACW)** a la imagen, lo que permite que ambos entornos invoquen el código entre sí.

Para obtener más información, consulte [arquitectura de Xamarin. Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[diagrama de la arquitectura de Xamarin. iOS ![](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Las aplicaciones de Xamarin. iOS se compilan por C# completo en el código del ensamblado de ARM nativo. Xamarin usa **selectores** para exponer Objective-c a C# administrados y **registradores** con C# el fin de exponer el código administrado a Objective-c. Los selectores y registradores se denominan colectivamente "enlaces" y permiten que Objective C# -C y se comuniquen.

Para obtener más información, consulte [arquitectura de Xamarin. iOS](~/ios/internals/architecture.md).

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin. Essentials es una biblioteca que proporciona API multiplataforma para características de dispositivos nativos. Al igual que el propio Xamarin, Xamarin. Essentials es una abstracción que simplifica el proceso de acceso a la funcionalidad nativa. Algunos ejemplos de funcionalidad proporcionada por Xamarin. Essentials incluyen:

- Información del dispositivo
- Sistema de archivos
- Acelerómetro
- Marcador de teléfono
- Texto a voz
- Bloqueo de pantalla

Para obtener más información, consulte [Xamarin. Essentials](~/essentials/index.md).

### <a name="xamarinforms"></a>Xamarin.Forms

Xamarin. Forms es un marco de interfaz de usuario de código abierto. Xamarin. Forms permite a los desarrolladores compilar aplicaciones de iOS, Android y Windows desde un único código base compartido. Xamarin. Forms permite a los desarrolladores crear interfaces de usuario en XAML con código C#subyacente en. Estas interfaces de usuario se representan como controles nativos de rendimiento en cada plataforma. Algunos ejemplos de características proporcionadas por Xamarin. Forms son:

- Idioma de la interfaz de usuario de XAML
- Enlace de datos
- Gestos
- Efectos
- Aplicación de estilos

Para obtener más información, consulte [Xamarin. Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Primeros pasos

Las guías siguientes le ayudarán a compilar su primera aplicación con Xamarin:

- [Introducción a Xamarin. Forms](~/xamarin-forms/index.yml)
- [Introducción a Xamarin. Android](~/android/index.yml)
- [Introducción a Xamarin. iOS](~/ios/index.yml)
- [Introducción a Xamarin. Mac](~/mac/index.yml)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
