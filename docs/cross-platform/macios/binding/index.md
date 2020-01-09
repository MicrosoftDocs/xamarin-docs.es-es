---
title: Enlace de Objective-C
description: En este documento se proporcionan vínculos a varias guías que describen cómo C# crear enlaces a código de Objective-C, lo que permite a los desarrolladores usar bibliotecas de uso no estándar en aplicaciones de Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: b7764d63991ec636043982509319e7097ef2091b
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663263"
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

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Tutorial: enlazar una biblioteca de Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

En este artículo se proporciona un tutorial paso a paso para crear un proyecto de enlace con el proyecto de código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C como ejemplo. La biblioteca InfColorPicker proporciona un controlador de vistas reutilizable que permite al usuario seleccionar un color en función de su representación HSB, lo que facilita la selección de color. El objetivo Sharpie se usará para ayudar en el proceso de enlace.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Ejemplos de enlace](https://github.com/mono/monotouch-bindings)

Colección de enlaces de terceros que se pueden utilizar como referencia al crear nuevos proyectos de enlace.

## <a name="mac"></a>Mac

Siga las instrucciones de [enlace de Mac](~/mac/platform/binding.md) para enlazar bibliotecas de MacOS. Puede crear una nueva **biblioteca de enlaces de Mac** desde la ventana **nuevo proyecto** :

[![cuadro de diálogo nuevo proyecto de enlaces de Mac](images/new-bindings-library-sml.png)](images/new-bindings-library.png#lightbox)

## <a name="related-links"></a>Vínculos relacionados

- [Enlace de iOS](~/ios/platform/binding-objective-c/index.md)
- [Enlace de Mac](~/mac/platform/binding.md)
