---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a9a25cff30b9dfbed44e60faeae2f577d07940fc
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139222"
---
# <a name="what-is-xamarin"></a>¿Qué es Xamarin?

[![Capturas de pantallas de la aplicación Xamarin de ejemplo en iOS y Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin es una plataforma de código abierto para compilar aplicaciones modernas y con mejor rendimiento para iOS, Android y Windows con .NET. Xamarin es una capa de abstracción que administra la comunicación de código compartido con el código de plataforma subyacente. Xamarin se ejecuta en un entorno administrado que proporciona ventajas como la asignación de memoria y la recolección de elementos no utilizados.

Xamarin permite a los desarrolladores compartir un promedio del 90 % de la aplicación entre plataformas. Este patrón permite a los desarrolladores escribir toda la lógica de negocios en un solo lenguaje (o reutilizar el código de aplicación existente), pero conseguir un rendimiento y una apariencia nativos en cada plataforma.

Las aplicaciones de Xamarin se pueden escribir en PC o Mac, y compilar en paquetes de aplicación nativos, como un archivo **.apk** en Android o **.ipa** en iOS.

> [!NOTE]
> Para la compilación e implementación de aplicaciones para iOS, en la actualidad se necesita un equipo MacOS. Para más información sobre los requisitos de desarrollo, vea [Requisitos del sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>A quién va destinado Xamarin

Xamarin es para desarrolladores con los siguientes objetivos:

- Compartir código, pruebas y lógica de negocios entre plataformas.
- Escribir aplicaciones multiplataforma en C# con Visual Studio.

## <a name="how-xamarin-works"></a>Funcionamiento de Xamarin

![Diagrama de la arquitectura de Xamarin](what-is-xamarin-images/xamarin-architecture.png)

En el diagrama se muestra la arquitectura general de una aplicación Xamarin multiplataforma. Xamarin permite crear una interfaz de usuario nativa en cada plataforma y escribir lógica de negocios en C# que se comparte entre plataformas. En la mayoría de los casos, con Xamarin se puede compartir el 80 % del código de la aplicación.

Xamarin se basa en **Mono**, una versión de código abierto de .NET Framework que se basa en los estándares ECMA de .NET. Mono ha existido desde hace casi tanto tiempo como .NET Framework y se ejecuta en la mayoría de plataformas, como Linux, Unix, FreeBSD y macOS. El entorno de ejecución Mono controla de forma automática tareas como la asignación de memoria, la recolección de elementos no utilizados y la interoperabilidad con las plataformas subyacentes.

Para más información sobre la arquitectura específica de la plataforma, vea [Xamarin.Android](#xamarinandroid) y [Xamarin.iOS](#xamarinios).

### <a name="added-features"></a>Características agregadas

Xamarin combina las funciones de las plataformas nativas y agrega una serie de características, entre las que se incluyen las siguientes:

1. **Enlaces completos para los SDK subyacentes**: Xamarin contiene enlaces para casi todos los SDK de plataforma subyacentes en iOS y Android. Además, estos enlaces están fuertemente tipados, lo que significa que la navegación y el uso son fáciles y que proporcionan una sólida comprobación de tipos en tiempo de compilación y durante el desarrollo. Los enlaces fuertemente tipados producen menos errores en tiempo de ejecución y aplicaciones de mayor calidad.
1. **Interoperabilidad con Objective-C, Java, C y C++** : Xamarin ofrece funciones para invocar directamente bibliotecas de Objective-C, Java, C y C++, lo que permite usar una amplia variedad de código de terceros. Estas funciones permiten usar bibliotecas existentes de iOS y Android escritas en Objective-C, Java, C o C++. Además, Xamarin ofrece proyectos de enlace que permiten enlazar bibliotecas nativas de Objective-C y Java mediante una sintaxis declarativa.
1. **Construcciones de lenguaje moderno**: las aplicaciones Xamarin se escriben en C#, un lenguaje moderno que incluye mejoras considerables con respecto a Objective-C y Java, como características de lenguaje dinámico, construcciones funcionales como lambdas, LINQ, características de programación en paralelo, genéricos y mucho más.
1. **Biblioteca de clases base (BCL) sólida**: las aplicaciones Xamarin usan la BCL de .NET, una gran colección de clases con características completas y optimizadas, como una compatibilidad eficaz con XML, bases de datos, serialización, E/S, cadenas, redes, etc. El código de C# existente se puede compilar para usarlo en una aplicación, lo que proporciona acceso a miles de bibliotecas que agregan funcionalidad más allá de la BCL.
1. **Entorno de desarrollo integrado (IDE) moderno**: Xamarin usa Visual Studio, un IDE moderno que incluye características como la finalización automática de código, un sofisticado sistema de administración de proyectos y soluciones, una exhaustiva biblioteca de plantillas de proyecto, control de código fuente integrado, etc.
1. **Compatibilidad multiplataforma móvil**: Xamarin ofrece una compatibilidad multiplataforma sofisticada con las tres principales plataformas: iOS, Android y Windows. Se pueden escribir aplicaciones de modo que compartan hasta el 90 % del código, y Xamarin.Essentials ofrece una API unificada para acceder a recursos comunes de las tres plataformas. El código compartido puede reducir considerablemente los costos de desarrollo y el tiempo de salida al mercado para los desarrolladores para dispositivos móviles.

### <a name="xamarinandroid"></a>Xamarin.Android

[![Diagrama de la arquitectura de Xamarin.Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Las aplicaciones Xamarin.Android se compilan desde C# en **lenguaje intermedio (IL)** que, después, se compila **Just-in-Time (JIT)** en un ensamblado nativo cuando se inicia la aplicación. Las aplicaciones Xamarin.Android se ejecutan en el entorno de ejecución Mono, en paralelo con la máquina virtual en tiempo de ejecución de Android (ART). Xamarin proporciona enlaces .NET a los espacios de nombres Android.* y Java.*. El entorno de ejecución Mono llama a estos espacios de nombres a través de **contenedores administrados que se pueden llamar (MCW)** y proporciona a ART **contenedores que se pueden llamar de Android (ACW)** , lo que permite que ambos entornos invoquen código entre sí.

Para más información, vea [Arquitectura de Xamarin.Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[![Diagrama de la arquitectura de Xamarin.iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Las aplicaciones Xamarin.iOS se compilan **Ahead-of-Time (AOT)** de forma completa desde código de C# en código de ensamblado de ARM nativo. Xamarin usa **selectores** para exponer Objective-C en código de C# administrado y **registradores** para exponer código de C# administrado en Objective-C. Los selectores y registradores se denominan colectivamente "enlaces" y permiten la comunicación entre Objective-C y C#.

Para más información, vea [Arquitectura de Xamarin.iOS](~/ios/internals/architecture.md).

### Xamarin.Essentials

Xamarin.Essentials es una biblioteca que proporciona API multiplataforma para características de dispositivos nativos. Como el propio Xamarin, Xamarin.Essentials es una abstracción que simplifica el proceso de acceso a la funcionalidad nativa. Algunos ejemplos de la funcionalidad proporcionada por Xamarin.Essentials son los siguientes:

- Información del dispositivo
- Sistema de archivos
- Acelerómetro
- Marcador telefónico
- Texto a voz
- Bloqueo de pantalla

Para obtener más información, vea [Xamarin.Essentials](~/essentials/index.md).

### Xamarin.Forms

Xamarin.Forms es un marco de interfaz de usuario de código abierto. Xamarin.Forms permite a los desarrolladores compilar aplicaciones iOS, Android y Windows desde un único código base compartido. Xamarin.Forms permite a los desarrolladores crear interfaces de usuario en XAML con código subyacente en C#. Estas interfaces de usuario se representan como controles nativos con mejor rendimiento en cada plataforma. Algunos ejemplos de las características que proporciona Xamarin.Forms son los siguientes:

- Lenguaje de la interfaz de usuario de XAML
- Enlace de datos
- Gestos
- Efectos
- Aplicación de estilos

Para obtener más información, vea [Xamarin.Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Primeros pasos

Las guías siguientes le ayudarán a compilar la primera aplicación con Xamarin:

- [Introducción a Xamarin.Forms](~/xamarin-forms/index.yml)
- [Introducción a Xamarin.Android](~/android/index.yml)
- [Introducción a Xamarin.iOS](~/ios/index.yml)
- [Introducción a Xamarin.Mac](~/mac/index.yml)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
