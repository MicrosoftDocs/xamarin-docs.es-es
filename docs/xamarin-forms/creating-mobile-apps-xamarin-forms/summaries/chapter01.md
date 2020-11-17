---
title: Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?
description: 'Creación de aplicaciones móviles con Xamarin.Forms: Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1af4b990d388436c04a2feb0abe3d4e3a8bc6756
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370460"
---
# <a name="summary-of-chapter-1-how-does-no-locxamarinforms-fit-in"></a>Resumen del capítulo 1. ¿Cómo encaja Xamarin.Forms?

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Este libro se publicó en la primavera de 2016 y no se ha actualizado desde entonces. Gran parte del libro sigue siendo útil, pero algunos de los materiales están anticuados y algunos temas ya no son completamente correctos o completos.

Uno de los trabajos menos agradables en programación es la migración de una base de código de una plataforma a otra, especialmente si esa plataforma implica un lenguaje de programación diferente. Existe la tentación, al migrar el código, de refactorizarlo también, pero si ambas plataformas deben mantenerse en paralelo, las diferencias entre las dos bases de código harán que el mantenimiento futuro sea más difícil.

## <a name="cross-platform-mobile-development"></a>Desarrollo móvil multiplataforma

Este problema es común cuando se destina a plataformas móviles. Actualmente, existen dos plataformas móviles principales, la familia Apple de iPhone e iPad que ejecutan el sistema operativo iOS y el sistema operativo Android que se ejecuta en una gran variedad de teléfonos y tabletas. Otra plataforma importante es la Plataforma universal de Windows (UWP) de Microsoft, que permite que un único programa tenga como destino Windows 10.

Un proveedor de software que desee dirigirse a estas plataformas debe tratar con diferentes paradigmas de interfaz de usuario, tres entornos de desarrollo diferentes, tres interfaces de programación diferentes y &mdash;quizás lo más incómodo&mdash;tres lenguajes de programación distintos: Objective-C para iPhone y iPad, Java para Android y C# para Windows.

## <a name="the-c-and-net-solution"></a>La solución C# y .NET

Aunque Objective-C, Java y C# se derivan del lenguaje de programación C, han evolucionado con rutas de acceso muy diferentes. C# es el más reciente de estos lenguajes y ha ido madurando de manera muy útil. Además, C# está estrechamente asociado a una infraestructura de programación completa denominada .NET, que proporciona compatibilidad para matemáticas, depuración, reflexión, colecciones, globalización, E/S de archivos, redes, seguridad, subprocesos, servicios web, administración de datos y lectura y escritura de XML y JSON.

Xamarin proporciona actualmente herramientas para el destino de las API nativas de Mac, iOS y Android mediante C# y .NET. Estas herramientas se denominan Xamarin.Mac, Xamarin.iOS y Xamarin.Android, que se conocen colectivamente como la plataforma Xamarin. Se trata de bibliotecas y enlaces que expresan las API nativas de estas plataformas con expresiones .NET.

Los desarrolladores pueden usar la plataforma Xamarin para escribir aplicaciones C# en Mac, iOS o Android de destino. Pero cuando se destina a más de una plataforma, tiene mucho sentido compartir parte del código entre las plataformas de destino. Esto implica separar el programa en código dependiente de la plataforma (por lo general, con la interfaz de usuario) y código independiente de la plataforma, que generalmente solo requiere la plataforma .NET base. Este código independiente de la plataforma puede residir en una biblioteca de clases portable (PCL) o en un proyecto compartido, que a menudo se denomina proyecto de recursos compartidos o SAP.

> [!NOTE]
> Las bibliotecas de clases portables se han sustituido por las bibliotecas .NET estándar. Todo el código de ejemplo del libro se ha convertido para usar bibliotecas .NET Standard.

## <a name="introducing-no-locxamarinforms"></a>Presentación de Xamarin.Forms

Cuando el destino son varias plataformas móviles, Xamarin.Forms permite compartir aún más el código. Un único programa escrito para Xamarin.Forms puede tener como destino estas plataformas:

- iOS para programas que se ejecutan en iPhone, iPad y iPod Touch
- Android para programas que se ejecutan en teléfonos y tabletas Android
- Plataforma universal de Windows para tener como destino Windows 10

> [!NOTE]
> Xamarin.Forms ya no admite Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, pero las aplicaciones de Xamarin.Forms se ejecutan en Windows 10 Desktop. También hay compatibilidad con la versión preliminar de las plataformas [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK#](~/xamarin-forms/platform/other/gtk.md) y [Tizen](~/xamarin-forms/platform/other/tizen.md).

La mayor parte de un programa de Xamarin.Forms se encuentra en una biblioteca o en SAP. Cada una de las plataformas consta de un pequeño código auxiliar de aplicación que llama a este código compartido.

Las API de Xamarin.Forms se asignan a controles nativos en cada plataforma, de modo que cada plataforma mantiene su aspecto característico:

[![Captura de pantalla triple del uso compartido de objetos visuales de la plataforma](images/ch01fg03-small.png "Xamarin.Forms Controles en cada plataforma")](images/ch01fg03-large.png#lightbox "Xamarin.Forms Controles en cada plataforma")

Las capturas de pantallas de izquierda a derecha muestran un teléfono iPhone y uno Android:

En cada pantalla, la página contiene una Xamarin.Forms[`Label` de ](xref:Xamarin.Forms.Label) para mostrar texto, un [`Button`](xref:Xamarin.Forms.Button) para iniciar acciones, un [`Switch`](xref:Xamarin.Forms.Switch) para elegir un valor activado o desactivado y un [`Slider`](xref:Xamarin.Forms.Slider) para especificar un valor dentro de un intervalo continuo. Las cuatro vistas son elementos secundarios de un objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout) en un objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

También se adjunta a la página una barra de herramientas de Xamarin.Forms que consta de varios objetos [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). Son visibles como iconos en la parte superior de las pantallas de iOS y Android, y en la parte inferior de la pantalla de Windows 10 Mobile.

Xamarin.Forms también admite el lenguaje XAML (lenguaje de marcado de aplicaciones extensible) desarrollado en Microsoft para varias plataformas de aplicaciones. Todos los objetos visuales del programa mostrados anteriormente se definen en XAML, tal como se muestra en el ejemplo [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals).

Un programa de Xamarin.Forms puede determinar en qué plataforma se está ejecutando y ejecutar código diferente en consecuencia. Lo que es más eficaz, los desarrolladores pueden escribir código personalizado para las distintas plataformas y ejecutar ese código desde un programa de Xamarin.Forms de manera independiente de la plataforma. Los desarrolladores también pueden crear controles adicionales escribiendo representadores para cada plataforma.

Aunque Xamarin.Forms es una buena solución para las aplicaciones de línea de negocio, para la creación de prototipos o para la realización de una demostración de prueba de concepto rápida, es menos idónea para las aplicaciones que requieren gráficos vectoriales o interacción táctil compleja.

## <a name="your-development-environment"></a>Su entorno de desarrollo

Su entorno de desarrollo depende de las plataformas a las que desea dirigirse y de las máquinas que quiere usar.

Si desea tener como destino iOS, necesitará un equipo Mac con Xcode y la plataforma Xamarin instalada. La compatibilidad con Android también requiere la instalación de Java y los SDK necesarios. Después, puede tener como destino iOS y Android mediante Visual Studio para Mac.

La instalación de Visual Studio permite en el equipo de destino iOS, Android y todas las plataformas de Windows. Sin embargo, para la compatibilidad con iOS desde Visual Studio todavía es necesario un equipo Mac con Xcode y la plataforma Xamarin instalada.

Puede probar los programas en un dispositivo real conectado mediante USB al equipo o en un simulador.

## <a name="installation"></a>Instalación

Antes de crear y compilar una aplicación de Xamarin.Forms, debe intentar crear y compilar por separado una aplicación iOS, una aplicación Android y una aplicación UWP, en función de las plataformas que quiera establecer como destino y el entorno de desarrollo.

Los sitios web de Xamarin y Microsoft contienen información sobre cómo hacerlo:

- [Introducción a iOS](~/ios/get-started/index.md)
- [Introducción a Android](~/android/get-started/index.md)
- [Centro de desarrollo de Windows](https://dev.windows.com)

Cuando pueda crear y ejecutar proyectos para estas plataformas individuales, no debería tener ningún problema al crear y ejecutar una aplicación de Xamarin.Forms.

## <a name="related-links"></a>Vínculos relacionados

- [Texto completo del capítulo 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Ejemplo del capítulo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
