---
title: Qué es Xamarin. Forme?
description: En este artículo se presentan Xamarin. Formularios y bibliotecas relacionadas.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: c217acdc3bd5c007822cc29fc730df99e373ba01
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607846"
---
# <a name="what-is-opno-locxamarinforms"></a>Qué es Xamarin. Forme?

[![capturas de pantallas del ejemplo [! Operador. NO-LOC (Xamarin)]. Aplicación de formularios en iOS y Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin. Forms es un marco de interfaz de usuario de código abierto. Xamarin. Forms permite a los desarrolladores compilar aplicaciones de Android, iOS y Windows desde un único código base compartido.

Xamarin. Forms permite a los desarrolladores crear interfaces de usuario en XAML con código C#subyacente en. Estas interfaces se representan como controles nativos de rendimiento en cada plataforma.

## <a name="who-opno-locxamarinforms-is-for"></a>Quién Xamarin. Forms es para

Xamarin. Forms es para desarrolladores con los siguientes objetivos:

- Comparta el diseño y el diseño de la interfaz de usuario entre plataformas.
- Comparta código, pruebas y lógica de negocios entre plataformas.
- Escriba aplicaciones multiplataforma en C# con Visual Studio.

## <a name="how-opno-locxamarinforms-works"></a>Cómo Xamarin. Formularios de trabajo

![[! Operador. NO-LOC (Xamarin)]. Diagrama de arquitectura de formularios](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin. Forms proporciona una API coherente para crear elementos de interfaz de usuario entre plataformas. Esta API se puede implementar en XAML o C# y admite el enlace de enlaces para patrones como Model-View-VIEWMODEL (MVVM).

En tiempo de ejecución, Xamarin. Forms utiliza los representadores de plataforma para convertir los elementos de interfaz de usuario multiplataforma en controles nativos en Android, iOS y UWP. Permite a los desarrolladores obtener una apariencia y un funcionamiento nativos, al tiempo que se obtienen las ventajas del uso compartido de código entre plataformas.

Xamarin. Normalmente, las aplicaciones de formularios están compuestas por una biblioteca .NET Standard compartida y proyectos de plataforma individuales. La biblioteca compartida contiene el XAML o C# las vistas y cualquier lógica de negocios, como servicios, modelos u otro código. Los proyectos de plataforma contienen cualquier lógica o paquete específico de la plataforma que la aplicación requiera.

Xamarin. Forms usa Xamarin para ejecutar aplicaciones .NET de forma nativa en todas las plataformas. Para obtener más información acerca de Xamarin, vea [¿Qué es Xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Herramientas adicionales

Xamarin. Forms tiene un gran ecosistema de paquetes NuGet que agregan diversas funciones a las aplicaciones. En esta sección se describen algunos paquetes de NuGet usados con frecuencia.

### <a name="opno-locxamarinessentials"></a>Xamarin. Essentials

Xamarin. Essentials es una biblioteca que proporciona API multiplataforma para características de dispositivos nativos. Al igual que Xamarin, Xamarin. Essentials es una abstracción que simplifica el proceso de acceso a utilidades nativas. Algunos ejemplos de utilidades proporcionadas por Xamarin. Entre los aspectos básicos se incluyen:

- Información del dispositivo
- Sistema de archivos
- Acelerómetro
- Marcador de teléfono
- Texto a voz
- Bloqueo de pantalla

Para obtener más información, vea [Xamarin. Aspectos básicos](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin. El shell de formularios reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones. Algunos ejemplos de características proporcionadas por el shell incluyen:

- Experiencia de navegación común
- Esquema de navegación basado en URI
- Controlador de búsqueda integrado

Para obtener más información, vea [Xamarin. Shell de formularios](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Características específicas de la plataforma

Xamarin. Forms proporciona una API común que representa controles nativos entre plataformas, pero una plataforma específica puede tener funcionalidad que no existe en otras plataformas. Por ejemplo, la plataforma Android tiene funcionalidad nativa para el desplazamiento rápido en un `ListView` pero iOS no. Xamarin. Los tipos específicos de la plataforma de formularios permiten utilizar funciones que solo están disponibles en una plataforma específica sin necesidad de crear representadores ni efectos personalizados.

Xamarin. Forms incluye soluciones prediseñadas para una variedad de funcionalidades específicas de la plataforma. Para obtener más información, vea:

- [Xamarin. Características de la plataforma de formularios](~/xamarin-forms/platform/platform-specifics/index.md)
- [Características específicas de la plataforma Android](~/xamarin-forms/platform/android/index.md)
- [características específicas de la plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Características específicas de la plataforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Objeto visual de material

Xamarin. El material visual de formularios se usa para aplicar reglas de diseño de materiales a Xamarin. Aplicaciones de formularios. Xamarin. El material visual de formularios utiliza la propiedad visual para aplicar de forma selectiva los representadores personalizados a la interfaz de usuario, lo que da lugar a una aplicación con una apariencia coherente entre iOS y Android.

Para obtener más información, vea [Xamarin. Material visual de formularios](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin. Forme](~/xamarin-forms/index.yml)
- [Xamarin. Essentials](~/essentials/index.md)
- [Xamarin. Shell de formularios](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin. Material visual de formularios](~/xamarin-forms/user-interface/visual/material-visual.md)
