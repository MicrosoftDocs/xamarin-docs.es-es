---
title: ¿Qué es Xamarin.Forms?
description: En este artículo se presentan Xamarin.Forms y las bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: aaceb6089a5b7e5f0551dafe9ef1fe50d01433d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "77374028"
---
# <a name="what-is-xamarinforms"></a>¿Qué es Xamarin.Forms?

[![Capturas de pantallas de la aplicación Xamarin.Forms de ejemplo en iOS y Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Forms es un marco de interfaz de usuario de código abierto. Xamarin.Forms permite a los desarrolladores compilar aplicaciones iOS, Android y Windows desde un único código base compartido.

Xamarin.Forms permite a los desarrolladores crear interfaces de usuario en XAML con código subyacente en C#. Estas interfaces se representan como controles nativos con mejor rendimiento en cada plataforma.

## <a name="who-xamarinforms-is-for"></a>A quién va destinado Xamarin.Forms

Xamarin.Forms es para desarrolladores con los siguientes objetivos:

- Compartir el diseño de la interfaz de usuario entre plataformas.
- Compartir código, pruebas y lógica de negocios entre plataformas.
- Escribir aplicaciones multiplataforma en C# con Visual Studio.

## <a name="how-xamarinforms-works"></a>Funcionamiento de Xamarin.Forms

![Diagrama de la arquitectura de Xamarin.Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Forms proporciona una API coherente para crear elementos de interfaz de usuario entre plataformas. Esta API se puede implementar en XAML o C#, y admite el enlace de datos para patrones como Model-View-ViewModel (MVVM).

En tiempo de ejecución, Xamarin.Forms usa los representadores de plataforma para convertir los elementos de la interfaz de usuario multiplataforma en controles nativos para Android, iOS y UWP. Esto permite a los desarrolladores obtener una apariencia y un funcionamiento nativos, además de las ventajas del uso compartido de código entre plataformas.

Las aplicaciones Xamarin.Forms suelen estar compuestas de una biblioteca compartida de .NET Standard y proyectos de plataforma individuales. La biblioteca compartida contiene las vistas XAML o C#, y cualquier lógica de negocios, como servicios, modelos u otro código. Los proyectos de plataforma contienen cualquier lógica o paquete específico de la plataforma que la aplicación necesite.

Xamarin.Forms usa Xamarin para ejecutar aplicaciones .NET de forma nativa en todas las plataformas. Para más información sobre Xamarin, vea [¿Qué es Xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Herramientas adicionales

Xamarin.Forms tiene un gran ecosistema de paquetes NuGet que agregan diversas funciones a las aplicaciones. En esta sección se describen algunos de los paquetes NuGet usados con frecuencia.

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin.Essentials es una biblioteca que proporciona API multiplataforma para características de dispositivos nativos. Como el propio Xamarin, Xamarin.Essentials es una abstracción que simplifica el proceso de acceso a utilidades nativas. Algunos ejemplos de las utilidades proporcionadas por Xamarin.Essentials son los siguientes:

- Información del dispositivo
- Sistema de archivos
- Acelerómetro
- Marcador telefónico
- Texto a voz
- Bloqueo de pantalla

Para más información, vea [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que la mayoría de aplicaciones necesita. Algunos ejemplos de características proporcionadas por Shell son los siguientes:

- Experiencia de navegación común
- Esquema de navegación basado en URI
- Controlador de búsqueda integrado

Para más información, vea [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Características específicas de las plataformas

Xamarin.Forms proporciona una API común que representa controles nativos entre plataformas, pero una plataforma específica puede tener funciones que no existan en otras. Por ejemplo, la plataforma Android tiene funcionalidad nativa para el desplazamiento rápido en un objeto `ListView` pero iOS no. Las características específicas de la plataforma de Xamarin.Forms permiten usar funciones que solo están disponibles en una plataforma concreta sin necesidad de crear representadores ni efectos personalizados.

Xamarin.Forms incluye soluciones prediseñadas para distintas funcionalidades específicas de la plataforma. Para obtener más información, consulte:

- [Características específicas de la plataforma de Xamarin.Forms](~/xamarin-forms/platform/platform-specifics/index.md)
- [Características específicas de la plataforma Android](~/xamarin-forms/platform/android/index.md)
- [Características específicas de la plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Características específicas de la plataforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Objeto visual de material

El objeto visual de material de Xamarin.Forms se usa para aplicar reglas de diseño de materiales a aplicaciones de Xamarin.Forms. El objeto visual de material de Xamarin.Forms usa la propiedad Visual para aplicar de forma selectiva representadores personalizados a la interfaz de usuario, lo que da lugar a una aplicación con una apariencia coherente entre iOS y Android.

Para más información, vea [Objeto visual de material de Xamarin.Forms](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Shell de Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Objeto visual de material de Xamarin.Forms](~/xamarin-forms/user-interface/visual/material-visual.md)
