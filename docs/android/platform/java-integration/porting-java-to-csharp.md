---
title: Portabilidad de Java a C# para Xamarin.Android
description: Una tercera opción para usar Java en una aplicación de Xamarin.Android es portar el código fuente Java a C#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027194"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Portabilidad de Java a C# para Xamarin.Android

Este enfoque puede ser de interés para organizaciones que:

- **Están cambiando las pilas de tecnología de Java a C#.**
- **Deben mantener una versión C# y otra Java del mismo producto.**
- **Desean tener una versión de .NET de una biblioteca de Java popular.**

Hay dos maneras de portar código Java a C#. La primera forma es portar el código manualmente. Implica que deben hacerlo desarrolladores experimentados que entiendan .NET y Java y que estén familiarizados con las expresiones adecuadas de cada idioma. Este enfoque tiene más sentido para pequeñas cantidades de código o para organizaciones que quieren cambiar completamente de Java a C#.

La segunda metodología de portabilidad consiste en probar y automatizar el proceso mediante el uso de un convertidor de código, como [Sharpen](https://github.com/mono/sharpen). [Sharpen](https://github.com/mono/sharpen) es un convertidor de código abierto de Versant que se usó originalmente para portar el código para *db4o* de Java a C#. db4o es una base de datos orientada a objetos, desarrollada en Java y que luego se ha portado a .NET. El uso de un convertidor de código puede tener sentido para los proyectos que deben existir en ambos lenguajes y que requieran cierta paridad entre los dos.

Un ejemplo de cuándo tiene sentido una herramienta de conversión de código automatizada puede verse en el proyecto [ngit](https://github.com/mono/ngit).
Ngit es un puerto del proyecto de Java [jgit](https://eclipse.org/).
El propio jgit es una implementación Java del sistema de administración de código fuente [Git](https://git-scm.com/). Para generar código C# desde Java, los programadores de ngit usan un sistema automatizado personalizado para extraer el código Java de jgit, aplicar algunas revisiones para acomodar el proceso de conversión y, a continuación, ejecutar Sharpen, que genera el código C#. Esto permite que el proyecto ngit se beneficie del trabajo en curso continuo que se realiza en jgit.

A menudo, hay una cantidad de trabajo que no es trivial implicado en el arranque de una herramienta de conversión de código automatizada, lo que puede resultar un obstáculo para su uso. En muchos casos, puede ser más sencillo y fácil portar Java a C# a mano.

## <a name="related-links"></a>Vínculos relacionados

- [Herramienta de conversión Sharpen](https://github.com/mono/sharpen)
