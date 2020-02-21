---
title: Enlace de bibliotecas de Android Kotlin
description: En este documento se describe cómo C# crear enlaces para Kotlin código, lo que permite consumir bibliotecas nativas en una aplicación de Xamarin. Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77497969"
---
# <a name="bind-android-kotlin-libraries"></a>Enlace de bibliotecas de Android Kotlin

La plataforma Android, junto con sus lenguajes y herramientas nativos, está evolucionando constantemente y hay muchas bibliotecas de terceros desarrolladas con las últimas ofertas. Maximizar la reutilización de código y componentes es uno de los principales objetivos del desarrollo multiplataforma. La capacidad de reutilizar los componentes creados con Kotlin es cada vez más importante para los desarrolladores de Xamarin, ya que su popularidad entre los desarrolladores sigue creciendo. Es posible que ya esté familiarizado con el proceso de enlazar bibliotecas de [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) normales. Hay documentación adicional ahora disponible que describe el proceso de [enlace de una biblioteca de Kotlin](walkthrough.md), para que una aplicación de Xamarin la consuma de la misma manera. El propósito de este documento es describir un enfoque de alto nivel para crear un enlace Kotlin para Xamarin.

## <a name="high-level-approach"></a>Enfoque de alto nivel

Con Xamarin, puede enlazar cualquier biblioteca nativa de terceros para que pueda ser consumida por una aplicación de Xamarin. Kotlin es el nuevo lenguaje y la creación de enlaces para las bibliotecas compiladas con este lenguaje requiere algunos pasos y herramientas adicionales. Este enfoque implica los cuatro pasos siguientes:

1. Crear la biblioteca nativa
1. Preparación de los metadatos de Xamarin, que permite a las C# herramientas de Xamarin generar clases
1. Compilación de una biblioteca de enlace de Xamarin con la biblioteca nativa y los metadatos
1. Usar la biblioteca de enlace de Xamarin en una aplicación de Xamarin

En las secciones siguientes se describen estos pasos con detalles adicionales.

### <a name="build-the-native-library"></a>Crear la biblioteca nativa

El primer paso es obtener una biblioteca Kotlin nativa (paquete AAR, que es un archivo Android). Puede solicitarlo directamente de un proveedor o compilarlo usted mismo.

### <a name="prepare-the-xamarin-metadata"></a>Preparación de los metadatos de Xamarin

El segundo paso es preparar el archivo de transformación de metadatos, que usarán las herramientas de Xamarin para generar las C# clases respectivas. En el escenario de mejor caso, este archivo puede estar vacío donde se detectan todas las clases y se generan mediante las herramientas de Xamarin. En algunos casos, se debe aplicar la transformación de metadatos para generar el código C# correcto o deseado. En muchos casos, se debe usar un desensamblador de AAR, como [Java descompilador (JD)](http://java-decompiler.github.io/), para identificar las dependencias ocultas y las clases no deseadas que desee excluir de la lista C# final de clases que se van a generar. Los metadatos finales deben representar la interfaz pública en la que interactuará la aplicación de referencia de Xamarin. Android.

### <a name="build-a-xamarinandroid-binding-library"></a>Compilación de una biblioteca de enlace de Xamarin. Android

El tercer paso consiste en crear un proyecto especial: una biblioteca de enlace de Xamarin. Android. Incluye las bibliotecas de Kotlin como referencias nativas y la transformación de metadatos definida en el paso anterior. En el momento de la escritura, se requiere un proyecto de biblioteca de enlace de Android independiente para cada paquete de AAR al que se hace referencia. La biblioteca de enlace debe agregar el paquete [Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) para admitir la biblioteca estándar de Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Usar la biblioteca de enlace de Xamarin

El cuarto y el último paso es hacer referencia a la biblioteca de enlaces en una aplicación de Xamarin. Android. Al agregar una referencia a la biblioteca de enlace de Xamarin. Android, la aplicación de Xamarin podrá usar las clases de Kotlin expuestas desde dentro de ese paquete.

## <a name="walkthrough"></a>Tutorial

En el enfoque anterior se describen los pasos de alto nivel necesarios para crear un enlace Kotlin para Xamarin. Hay muchos pasos de nivel inferior implicados y otros detalles que se deben tener en cuenta al preparar estos enlaces en la práctica, incluida la adaptación a los cambios en las herramientas y los lenguajes nativos. La intención es ayudarle a obtener una comprensión más profunda de este concepto y de los pasos de alto nivel implicados en este proceso. Para obtener una guía paso a paso detallada, consulte la documentación del [tutorial de enlace de Xamarin Kotlin](walkthrough.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Android Studio](https://developer.android.com/studio)
- [Instalación de Gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)
- [Biblioteca de BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Enlazar biblioteca de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadatos de enlace de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [NuGet de Xamarin. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositorio del proyecto de ejemplo](https://github.com/xamcat/xamarin-binding-kotlin-framework)
