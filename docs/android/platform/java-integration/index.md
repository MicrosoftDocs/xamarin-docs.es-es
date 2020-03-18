---
title: Integración de Java con Xamarin.Android
description: El ecosistema de Java incluye una colección diversa e inmensa de componentes. Muchos de estos componentes se pueden usar para reducir el tiempo necesario para desarrollar una aplicación de Android. En este documento se presenta y proporciona información general de nivel superior de algunas de las formas en que los desarrolladores pueden usar estos componentes de Java existentes para mejorar la experiencia del desarrollo de aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020102"
---
# <a name="java-integration-with-xamarinandroid"></a>Integración de Java con Xamarin.Android

_El ecosistema de Java incluye una colección diversa e inmensa de componentes. Muchos de estos componentes se pueden usar para reducir el tiempo necesario para desarrollar una aplicación de Android. En este documento se presenta y proporciona información general de nivel superior de algunas de las formas en que los desarrolladores pueden usar estos componentes de Java existentes para mejorar la experiencia del desarrollo de aplicaciones de Xamarin.Android._

## <a name="overview"></a>Información general

Dada la extensión del ecosistema de Java, es muy probable que cualquier funcionalidad concreta necesaria para una aplicación de Xamarin.Android ya se haya codificado en Java. Por este motivo, es interesante probar y volver a usar las bibliotecas existentes al crear una aplicación de Xamarin.Android.

Hay tres formas posibles de volver a usar las bibliotecas de Java en una aplicación de Xamarin.Android: 

- **Crear una biblioteca de enlaces de Java**: con esta técnica, se usa un proyecto de Xamarin.Android para crear contenedores C# alrededor de los tipos de Java. Una aplicación de Xamarin.Android puede hacer referencia a los contenedores C# creados por este proyecto y, a continuación, usar el archivo `.jar`. 

- **Java Native Interface**: *Java Native* *Interface* (JNI) es un marco que permite a código que no es de Java (como C** o C#) llamar a código de Java o recibir llamadas de este código en una JVM. 

- **Portar el código**: este método implica tomar el código fuente Java y, a continuación, convertirlo en C#. Esto se puede hacer manualmente o mediante una herramienta automatizada como Sharpen. 

En el núcleo de las dos primeras técnicas está *Java Native Interface* (JNI). JNI es un marco que permite a las aplicaciones no escritas en Java interactuar con el código Java que se ejecuta en un Máquina virtual Java. Xamarin.Android usa JNI para crear *enlaces* para el código C#. 

La primera técnica es un enfoque declarativo más automatizado para enlazar bibliotecas de Java. Implica el uso de Visual Studio para Mac o de un tipo de proyecto de Visual Studio proporcionado por Xamarin.Android: la biblioteca de enlaces de Java. Para crear correctamente estos enlaces, es posible que una biblioteca de enlaces de Java requiera algunas modificaciones manuales, pero no tantas como las que requeriría con un enfoque de JNI puro. Consulte [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md) para obtener más información sobre las bibliotecas de enlaces de Java. 

La segunda técnica, el uso de JNI, funciona en un nivel mucho más bajo, pero puede proporcionar un mayor control y acceso a los métodos de Java que normalmente no serían accesibles a través de una biblioteca de enlaces de Java. 

La tercera técnica es radicalmente diferente de las dos anteriores: portar el código de Java a C#. Portar el código de un lenguaje a otro puede ser un proceso muy laborioso, pero es posible reducir ese esfuerzo con la ayuda de una herramienta llamada *Sharpen*. Sharpen es una herramienta de código abierto que es un convertidor de Java a C#. 

## <a name="summary"></a>Resumen

En este documento se proporciona información general de nivel superior sobre algunas de las distintas formas en que se pueden volver a usar las bibliotecas de Java en una aplicación de Xamarin.Android. Se introducen los conceptos de enlaces y contenedores que se pueden llamar administrados, y se explican las opciones para portar código Java a C#. 

## <a name="related-links"></a>Vínculos relacionados

- [Arquitectura](~/android/internals/architecture.md)
- [Enlace de una biblioteca Java](~/android/platform/binding-java-library/index.md)
- [Trabajo con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Dar nitidez](https://github.com/slluis/sharpen)
- [Java Native Interface](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
