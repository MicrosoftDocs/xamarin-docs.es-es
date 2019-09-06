---
title: Enlace de Objective-C
description: En este documento se proporcionan vínculos a varias guías que describen cómo C# crear enlaces a código de Objective-C, lo que permite a los desarrolladores usar bibliotecas de uso no estándar en aplicaciones de Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: conceptdev
ms.author: crdun
ms.date: 01/25/2016
ms.openlocfilehash: 8975bb899127d0761359bec695cd084f89c12972
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290559"
---
# <a name="binding-objective-c"></a>Enlace de Objective-C

En esta sección se incluye una serie de documentos que cubren la creación de enlaces a bibliotecas de Objective-C, de modo C# que se pueden llamar desde aplicaciones creadas con Xamarin. iOS o Xamarin. Mac.

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Información general](~/cross-platform/macios/binding/overview.md)

Este documento contiene algunos de los aspectos internos de cómo tiene lugar un enlace. Se trata de un documento avanzado con información técnica.

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

En este documento se describe el proceso que C# se usa para crear enlaces de API de Objective-c y cómo se asignan las expresiones en Objective-c a las expresiones usadas en .net.
Si va a enlazar solo las API de C, debe usar el mecanismo estándar de .NET para esto, el marco de trabajo P/Invoke.

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guía de referencia de la definición de enlace](~/cross-platform/macios/binding/binding-types-reference.md)

Esta es la guía de referencia que describe todos los atributos disponibles para enlazar autores para impulsar el proceso de generación de enlaces.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objective Sharpie es una herramienta de línea de comandos que ayuda a arrancar el primer paso de un enlace. Funciona mediante el análisis de los archivos de encabezado de una biblioteca nativa para asignar la API pública en la [definición de enlace](~/cross-platform/macios/binding/objective-c-libraries.md) (un proceso que también se puede hacer manualmente).

## <a name="ios"></a>iOS

La [Página enlace de iOS](~/ios/platform/binding-objective-c/index.md) vuelve a vincular a estos recursos de enlace comunes, además de los ejemplos siguientes.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Tutorial: Enlazar una biblioteca de Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

En este artículo se proporciona un tutorial paso a paso para crear un proyecto de enlace con el proyecto de código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C como ejemplo. La biblioteca InfColorPicker proporciona un controlador de vistas reutilizable que permite al usuario seleccionar un color en función de su representación HSB, lo que facilita la selección de color. El objetivo Sharpie se usará para ayudar en el proceso de enlace.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Ejemplos de enlace](https://github.com/mono/monotouch-bindings)

Colección de enlaces de terceros que se pueden utilizar como referencia al crear nuevos proyectos de enlace.

## <a name="mac"></a>Mac

El [enlace de Mac](~/mac/platform/binding.md) históricamente ha sido un proceso muy manual. Actualmente hay una [vista previa descargable](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) de la compatibilidad con proyectos de enlace de Mac para una futura versión de Visual Studio para Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Enlace de iOS](~/ios/platform/binding-objective-c/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
