---
title: Enlace de bibliotecas Kotlin de Android
description: En este documento se describe cómo crear enlaces de C# para código de Kotlin, lo que permite consumir bibliotecas nativas en una aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: d2251a0219ca25ecd37e42361e49ac966bac131d
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853001"
---
# <a name="bind-android-kotlin-libraries"></a>Enlace de bibliotecas Kotlin de Android

> [!IMPORTANT]
> Estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Realice [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar de esfuerzos de desarrollo futuros.

La plataforma Android, junto con sus lenguajes y herramientas nativos, está evolucionando constantemente y hay muchas bibliotecas de terceros desarrolladas con las últimas ofertas. La maximización de la reutilización del código y de los componentes es uno de los principales objetivos del desarrollo multiplataforma. La capacidad de reutilizar los componentes creados con Kotlin es cada vez más importante para los desarrolladores de Xamarin, ya que su popularidad entre los desarrolladores sigue creciendo. Es posible que ya esté familiarizado con el proceso de enlace de las bibliotecas de [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) normales. Ahora hay documentación adicional que describe el proceso de [enlazar una biblioteca de Kotlin](walkthrough.md), de modo que una aplicación de Xamarin la pueda utilizar de la misma manera. El objetivo de este documento es describir un enfoque de alto nivel para crear un enlace de Kotlin para Xamarin.

## <a name="high-level-approach"></a>Enfoque de alto nivel

Con Xamarin, puede enlazar cualquier biblioteca nativa de terceros para que una aplicación de Xamarin la pueda consumir. Kotlin es el nuevo lenguaje y la creación de enlaces para las bibliotecas compiladas con este lenguaje requiere algunos pasos y herramientas adicionales. Este enfoque implica los cuatro pasos siguientes:

1. Compilación de la biblioteca nativa
1. Preparación de los metadatos de Xamarin, que permite a las herramientas de Xamarin generar clases de C#
1. Compilación de una biblioteca de enlaces de Xamarin con la biblioteca nativa y los metadatos
1. Uso de la biblioteca de enlaces de Xamarin en una aplicación de Xamarin

En las secciones siguientes se describen estos pasos con detalles adicionales.

### <a name="build-the-native-library"></a>Compilación de la biblioteca nativa

El primer paso consiste en obtener una biblioteca de Kotlin nativa (un paquete AAR, que es un archivo Android). Puede solicitarla directamente de un proveedor o compilarla usted mismo.

### <a name="prepare-the-xamarin-metadata"></a>Preparación de los metadatos de Xamarin

El segundo paso consiste en preparar el archivo de transformación de metadatos, que usan las herramientas de Xamarin para generar las clases de C# respectivas. En el mejor caso, este archivo puede estar vacío cuando todas las clases se detectan y se generan mediante las herramientas de Xamarin. En algunos casos, se debe aplicar la transformación de metadatos para generar el código de C# correcto o deseado. En muchos casos, se debe usar un desensamblador AAR, como el [descompilador de Java (JD)](http://java-decompiler.github.io/), para identificar las dependencias ocultas y las clases no deseadas que desee excluir de la lista final de clases de C# que se va a generar. Los metadatos finales deben representar la interfaz pública en la que interactuará la aplicación de referencia de Xamarin.Android.

### <a name="build-a-xamarinandroid-binding-library"></a>Compilación de una biblioteca de enlaces de Xamarin.Android

El tercer paso consiste en crear un proyecto especial: una biblioteca de enlaces de Xamarin.Android. Incluye las bibliotecas de Kotlin como referencias nativas y la transformación de metadatos definida en el paso anterior. En el momento de redactar este documento, se requiere un proyecto de biblioteca de enlaces de Android independiente para cada paquete de AAR al que se hace referencia. La biblioteca de enlaces debe agregar el paquete [Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) para admitir la biblioteca estándar de Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Uso de la biblioteca de enlaces de Xamarin

El cuarto y el último paso consiste en hacer referencia a la biblioteca de enlaces en una aplicación de Xamarin.Android. Al agregar una referencia a la biblioteca de enlaces de Xamarin.Android, la aplicación de Xamarin podrá usar las clases de Kotlin expuestas desde dentro de ese paquete.

## <a name="walkthrough"></a>Tutorial

En el enfoque anterior se describen los pasos de alto nivel necesarios para crear un enlace de Kotlin para Xamarin. Hay muchos pasos de nivel inferior implicados y otros detalles que se deben tener en cuenta al preparar estos enlaces en la práctica, incluida la adaptación a los cambios en las herramientas y los lenguajes nativos. La intención es ayudarle a obtener una comprensión más profunda de este concepto y de los pasos de alto nivel implicados en este proceso. Para obtener una guía paso a paso detallada, consulte la documentación del [tutorial de enlace de Kotlin en Xamarin](walkthrough.md).

## <a name="related-links"></a>Vínculos relacionados

- [Android Studio](https://developer.android.com/studio)
- [Instalación de Gradle](https://gradle.org/install/)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Descompilador de Java](http://java-decompiler.github.io/)
- [Biblioteca de Kotlin de BubblePicker](https://github.com/igalata/Bubble-Picker)
- [Enlace de una biblioteca Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadatos de enlaces de Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repositorio del proyecto de ejemplo](https://github.com/xamcat/xamarin-binding-kotlin-framework)
